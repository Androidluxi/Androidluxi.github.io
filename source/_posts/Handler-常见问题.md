---
title: Handler 常见问题分析
index_img: /img/siteicon/handler2.jpg
date: 2024-10-21 23:46:52
category:
  - Android 进阶
tags:
  - Android
abbrlink: 4b72
---

<meta name="referrer" content="no-referrer"/>

## 1. 一个线程有几个 Handler？ 

一个线程可以有无数个handler。

## 2. 一个线程有几个 Looper？如何保证？ 

```java
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```

可以看到线程相关信息都由ThreadLocal管理，在ThreadLocal中存放了线程的唯一信息，它是线程唯一的标识。

## 3. Handler内存泄漏原因？ 为什么其他的内部类没有说过有这个问题？ （为什么Handler要使用static修饰？）

内部类默认持有外部类实例。当我们在Activity里面创建内部类MyHandler（继承Handler），那么在MyHandler内部是默认持有Activity.this，那么如果现在如果使用该handler发送了一个延迟5min时候执行的message，但是3min时候，activity执行了finish，结果因为Message中存在handler的引用，而handler又存在activity的引用，导致内存无法释放（内存泄漏）。

```java
private boolean enqueueMessage(@NonNull MessageQueue queue, @NonNull Message msg,long uptimeMillis) {
    msg.target = this; //message持有handler引用
    msg.workSourceUid = ThreadLocalWorkSource.getUid();
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);
 }
```

## 5. 为何主线程可以new Handler？如果想要在子线程中new Handler 要做些什么准备？ 

首先我们需要知道我们使用handler发送和处理消息需要哪些东西，MessageQueue、Handler 、Looper。MessageQueue是一个优先级队列，他的本质是一个单向链表。存放我们使用handler发送的消息，Handler则是我们消息的处理者，我们发送的消息会调用enqueueMessage方法将消息放到MessageQueue中，然后Looper会不断的去遍历MessageQueue中的消息，最后调用next方法取出消息，在Handler的handleMessage方法里面进行处理。这就是Handler的大致流程。我们在主线程里面通过new Handler创建了handler实例，那么还需要MessageQueue和Looper。既然我们在主线程中的handler能正常使用，说明Looper。MessageQueue肯定是存在的，当然是系统帮我们完成了创建。

```java
 //Base\core\java\android\app\ActivityThread.java
public static void main(String[] args) {
        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "ActivityThreadMain");
        // Install selective syscall interception
        AndroidOs.install();
		......
        Looper.prepareMainLooper();//创建Looper
        ......
}
```

```java
   private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);//创建和线程绑定的MessageQueue
        mThread = Thread.currentThread();
    }
```

满足了上述的条件，我们在主线程里面就能正常使用handler发送消息了。那么在子线程里面，这些操作就需要我们自己完成，初始化Looper，

创建handler，使Looper启动。

```java
Looper.prepare();
Log.d("TAG", "子线程: " + Thread.currentThread().getName() + ",looper = " + Looper.myLooper() + ",MessagesQueue = " + Looper.myQueue());
mSub_handler = new Handler(Objects.requireNonNull(Looper.myLooper()), new Handler.Callback() {
    @Override
    public boolean handleMessage(@NonNull Message msg) {
        Log.d(TAG, "mSub_handler : msg.what =" + msg.what);
        Log.d(TAG, "mSub_handler : msg.obj =" + msg.obj);
        return false;
    }
}) {
    @Override
    public void handleMessage(@NonNull Message msg) {
        super.handleMessage(msg);
        Log.d(TAG, "mSub_handler，线程 ：" + Thread.currentThread().getName() + " 处理消息 : msg.what =" + msg.what);
        Log.d(TAG, "mSub_handler，线程 ：" + Thread.currentThread().getName() + " 处理消息 : msg.obj =" + msg.obj);
    }
};
Looper.loop();
```

当然我们也可以使用HandlerThread，也可以创建子线程的handler。

```java
HandlerThread handlerThread = new HandlerThread("HandlerThread");
handlerThread.start();
Handler handler = new Handler(handlerThread.getLooper(), new Handler.Callback() {
    @Override
    public boolean handleMessage(@NonNull Message msg) {
        Log.d(TAG, "mSub_handler，线程 ：" + Thread.currentThread().getName() + " 处理消息 : msg.what =" + msg.what);
        Log.d(TAG, "mSub_handler，线程 ：" + Thread.currentThread().getName() + " 处理消息 : msg.obj =" + msg.obj);
        return true;
    }
});
```



## 6.为什么使用Message.obtain创建消息，有什么好处？

```java
Message msg1 = Message.obtain();//方式1
Message message = new Message();//方式2
```

这两种方式都是允许的，区别在方式1是内存复用，它并没有去新创建一个Message对象，而是使用已经创建好的空的Message对象。这样会避免大量的message创建导致内存抖动，最后OOM。

```java
  private static boolean loopOnce(final Looper me,final long ident, final int thresholdOverride) {
 		.......
          try {
            msg.target.dispatchMessage(msg);//消息分发给适合的handler处理
            if (observer != null) {
                observer.messageDispatched(token, msg);
            }
            dispatchEnd = needEndTime ? SystemClock.uptimeMillis() : 0;
        } catch (Exception exception) {
            if (observer != null) {
                observer.dispatchingThrewException(token, msg, exception);
            }
            throw exception;
        } finally {
            ThreadLocalWorkSource.restore(origWorkSource);
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }
        ........
        
        msg.recycleUnchecked();//这里并没有销毁message，而是回收消息

        return true;
 }
```

可以看到在下面的方法中，将message中下内容清空后，将该message放到了消息链表的头部。这里MAX_POOL_SIZE=50，最多维护的消息链表最长为50，如果链表已经满了，那么该message就会被垃圾回收机制处理。

```java
void recycleUnchecked() {
    // Mark the message as in use while it remains in the recycled object pool.
    // Clear out all other details.
    flags = FLAG_IN_USE;
    what = 0;
    arg1 = 0;
    arg2 = 0;
    obj = null;
    replyTo = null;
    sendingUid = UID_NONE;
    workSourceUid = UID_NONE;
    when = 0;
    target = null;
    callback = null;
    data = null;
    synchronized (sPoolSync) {
        if (sPoolSize < MAX_POOL_SIZE) {
            next = sPool;//将next指向消息链表头部的message
            sPool = this;//当前的message成为链表第一个message
            sPoolSize++;//链表变长
        }
    }
}
```

这里是obtaion方法的具体实现，可以看到取出了消息链表头部的消息，并断开与消息池中其他message的链接。将链表中第二个message作为链表的头部消息。如果消息池中不存在消息，就new一个新的消息。

```java
    public static Message obtain() {
        synchronized (sPoolSync) {//加锁，避免执取message实例的过程中，有新的message插入到消息链表中
            if (sPool != null) {
                Message m = sPool;
                sPool = m.next;
                m.next = null;
                m.flags = 0; // clear in-use flag
                sPoolSize--;
                return m;
            }
        }
        return new Message();
    }
```

从源码可以看到，Message内部是维护了一个消息池，这个消息池本质是一个单链表。我们使用Message.obtaion()获取消息会优先从消息池中获取，消息池中没有可用的消息才会取new消息。当消息被处理完之后，也并不会直接被GC回收，而且放到消息池中，消息池最大容量是50个message。这样的好处是避免了大量message的创建，然后回收，遗留的内存碎片，过多的内存碎片会导致虚拟机中没有大容量连续的内存地址，这样当我们想要申请一块连续的内存空间时候，就可能会出现OOM，这是我们应该极力避免的情况。这种对message的复用本质是享元设计模式，设计模式的思想应该融入我们的每一部分代码。

## 7.Handler源码中，为什么不少构造方法都废弃了？原因是什么？

```Java
@Deprecated
public Handler() {
    this(null, false);
}
```

```java
@Deprecated
public Handler(@Nullable Callback callback) {
    this(callback, false);
}
```

## 8.Looper死循环为什么不会导致应用卡死？

loop无限循环用于取出消息并将消息分发出去，没有消息时会阻塞在queue.next()里的nativePollOnce()方法里，并释放CPU资源进入休眠。Android的绝大部分操作都是通过Handler机制来完成的，如果没有消息，则不需要程序去响应，就不会有ANR。ANR一般是消息的处理过程中耗时太长导致没有及时响应用户操作。

## 9. Handler loop 休眠为什么不会导致ANR？

往Looper里面添加消息的时候，它会唤醒这个Looper。Looper 阻塞等待：queue.next() 方法会阻塞，直到有新的消息到达消息队列。这意味着如果没有新的消息，Looper 会进入休眠状态，不会占用 CPU 资源。Looper 非阻塞处理：一旦有新的消息到达，queue.next() 会立即返回，Looper 会继续处理消息。处理完消息后，Looper 会再次调用 queue.next()，进入下一次循环。所有的事件都能够及时响应，当然就不会ANR了。

## 10.同步屏障的原理和意义？

首先明确几个概念，同步消息，异步消息。我们日常使用handler发送的消息都是同步消息，顾名思义，同步屏障就是拦截同步消息的。为什么要拦截同步消息呢？当然是要给异步消息让步，让handler优先处理异步消息。我们先看看同步屏障是怎么实现的？

```JAVA
 Message next() {
 	.......
	 if (msg != null && msg.target == null) {
     	 // Stalled by a barrier.  Find the next asynchronous message in the queue.
   		  do {
     		 prevMsg = msg;
     		 msg = msg.next;
      		} while (msg != null && !msg.isAsynchronous());//如果是异步消息就跳出循环
  		}
	 ......
     return msg;
 }
```

可以看到同步屏障本质也是一个消息，但是是一个没有target的消息，也就是没有handler处理的消息，那么他是怎么出现在messageQueue里面的呢？这时候当然不能使用handler发送message，而是直接插入进去的。这是一个隐藏的方法。通过这个方法就会给MessageQueue中插入同步屏障，

```java
@UnsupportedAppUsage
@TestApi
public int postSyncBarrier() {
    return postSyncBarrier(SystemClock.uptimeMillis());
}

private int postSyncBarrier(long when) {
    // Enqueue a new sync barrier token.
    // We don't need to wake the queue because the purpose of a barrier is to stall it.
    synchronized (this) {
        final int token = mNextBarrierToken++;
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;
	   //就是这里！！！初始化Message对象的时候，并没有给target赋值，因此 target==null
        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when <= when) {
                //如果开启同步屏障的时间（假设记为T）T不为0，且当前的同步消息里有时间小于T，则prev也不为null
                prev = p;
                p = p.next;
            }
        }
        //根据prev是不是为null，将 msg 按照时间顺序插入到 消息队列（链表）的合适位置
        if (prev != null) { // invariant: p == prev.next
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }
        return token;
    }
}
```

怎么设置消息成为异步消息呢？异步消息就我们想要立刻执行的消息，一般UI相关的消息应该是立刻执行的，所以我们可以在View相关的类可以看到如下代码：

```java
	//core\java\android\view\ViewRootImpl.java
    private void scheduleProcessInputEvents() {
        if (!mProcessInputEventsScheduled) {
            mProcessInputEventsScheduled = true;
            Message msg = mHandler.obtainMessage(MSG_PROCESS_INPUT_EVENTS);
            msg.setAsynchronous(true);//设置异步消息
            mHandler.sendMessage(msg);
        }
    }
```

为了让消息尽快执行，为什么不直接将消息插入到MessagQueue头部呢？这是因为保证消息顺序：如果直接将消息插入队列的最头部，会破坏消息的顺序。在很多应用场景中，消息的处理顺序是非常重要的，特别是涉及到状态管理和依赖关系时。同步屏障的目的是在特定时间点前阻止其他消息的处理，而不是完全改变消息的处理顺序。同步屏障的作用：同步屏障的作用是在某个时间点前阻止其他消息的处理，确保在这个时间点之前的所有消息都已经被处理完毕。如果将同步屏障直接插入队列的最头部，可能会导致后续的消息被无条件地延迟处理，这与同步屏障的设计初衷不符直接将消息插入队列的最头部可能会导致频繁的队列重组，增加不必要的性能开销。同步屏障通过在合适的位置插入，可以更高效地管理消息队列，减少不必要的资源消耗。
