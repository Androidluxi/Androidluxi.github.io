---
title: IPC基础知识
index_img: https://s2.loli.net/2024/02/19/m7SKdMnGWipElZx.png
category:
  - Android
  - IPC
tags:
  - Android
abbrlink: 5f3e
date: 2024-01-18 22:47:56
---

<meta name="referrer" content="no-referrer"/>

# Android 中的IPC机制

## 1.  Android IPC简介

> IPC是Inter-Process Communication的缩写，含义为**进程间通信**或者**跨进程通信**，是指两个进程之间进行数据交换的过程。

**线程是CPU调度的最小单元，同时线程是一种有限的系统资源。而进程一般指一个执行单元，在PC和移动设备上指一个程序或者一个应用。**一个进程可以包含多个线程，因此进程和线程是包含与被包含的关系。

🎈最简单的情况下，一个进程中可以只有一个线程，即主线程。在Android里面主线程也叫UI线程，在UI线程里才能操作界面元素。

很多时候，一个进程中需要执行大量耗时的任务，如果这些任务放在主线程中去执行就会造成界面无法响应，严重影响用户体验，在Android中有一个特殊的名字叫做ANR(Application Not Responding)，即应用无响应。解决这个问题就需要用到线程，把一些耗时的任务放在线程中完成。

IPC不是Android中独有的，任何一个操作系统都需要有相应的IPC机制。对于Android来说，它是一种基于Linux内核的移动操作系统，它的进程间通信并不能完全继承自Linux。在Android中最有特色的进程间通信方式是Binder，通过Binder可以轻松地实现进程间通信。除了Binder，Android还支持Socket，通过Socket也可以实现任意两个终端之间的通信。

说到IPC的使用场景就必须提到多进程，只有面对多进程这种场景下，才需要考虑进程间通信。

### 1.1 开启一个进程

通过在四大组件中指定android:process 开启多进程,以下两种方式都可以开启一个进程

```XML
<activity android:name=".FirstActivity"
		  android:process=":remote"/>
<activity android:name=".SecondActivity"
		  android:process="com.luxi.LaunchMode:remote"/>	
```

两种开启方法有一点区别：

1. `：`的含义表示，要在当前进程的前面加上当前的包名，这只是一种简写。
2. `：开头的进程属于当前应用的私有进程。其他应用的组件不可以和他运行在同一个进程中。
3. `com.luxi.LaunchMode:remote`这种命名方式则是全局进程，其他应用可以通过ShareUID方式可以和他运行在同一个进程下。

🚑可以这样理解：

1. 如果有两个应用（包名不同），他们具有相同的Share UID，并且签名相同。那么就可以在某些方面共享数据和权限。比如data目录、组件信息。

   比如将apk打成系统apk，需要指定`android:sharedUserId="android.uid.system"`，然后还需要系统签名。

2. 通过修改process属性，将两个apk（**Share User Id相同**）指定绑定到进程名为com.demo，那么这两个apk可以视为一个应用的两个部分，内存共享。

[通过共享用户ID来实现多个应用程序使用同一个进程（一些情况的测试） - 午夜稻草人 - 博客园 (cnblogs.com)](https://www.cnblogs.com/scarecrow-blog/p/4876560.html)

### 1.2 多进程模式的运行机制

1. **共享用户标识 (Share User Id):** 共享用户标识允许两个或多个应用共享相同的用户 ID 和数据。这意味着它们可以访问并修改对方的数据（前提是有相应的权限）。这在一些特定的应用场景中可能很有用，例如同一个开发者创建的多个应用之间共享用户数据。
2. **包名不同:** 尽管两个 APK 共享用户标识，但它们的包名是不同的。包名是 Android 系统用于唯一标识每个应用的字符串。由于包名不同，系统会将这两个应用视为不同的应用。
3. **签名密钥相同:** 由于签名密钥相同，这意味着这两个应用都来自同一个开发者，并且它们可以共享一些签名密钥相关的特性，例如共享相同的数字证书。

🎄**同一个应用间的多进程**：**它就相当于两个不同的应用采用了SharedUID的模式**

使用多进程会造成如下几个方面的问题：

- 静态成员和单例模式完全失效
- 线程同步机制完全失效
- SharedPreferences的可靠性下降
- Application会多次创建

## 2. 进程间的通信原理

### 2.1 Linux传统的进程间通信

<div align=center><img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240118223942.png"></div>



1. 消息发送方将要发送的数据存放在内存缓存区中
2. 通过系统调用进入内核态
3. 内核程序在内核空间分配内存,开辟一块内核缓存区
4. 调用copy_from_user()函数将数据从用户空间的内存缓冲区拷贝到内核空间的内核缓冲区中
5. 接收方进程在接收数据时在自己的用户空间开辟一块内存缓冲区
6. 接收方进程接收数据

### 2.2 Android中的Binder跨进程通信

<div align=center><img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240118223718.png"/></div>



1. 首先Binder驱动在内核空间创建一个数据接收的缓存区
2. 在内核空间开辟一块内核缓存区,建立内核缓存区和内核中数据接收缓存区之间的映射关系,以及内核中数据接收缓存区和接收进程用户空间地址的映射关系
3. 当发送方进程通过系统调用copy_from_user()将数据copy到内核缓存区时,由于内核缓存区和接收数据的用户空间存在映射关系,所以就直接把数据发送到了接收进程的用户空间,完成了一次进程间的通信.

## 3.Android 中进程间通信的方式

常见的有下面几种方式：

1. 使用Bundle的方式

2. 使用文件共享的方式

3. 使用Messenger的方式

4. 使用AIDL的方式

5. 使用ContentProvider的方式

6. 使用广播接收者（Broadcast）的方式

7. 使用Socket的方式

   

### 3.1  使用Bundle的方式

```java
                Bundle bundle = new Bundle();
                bundle.putString("name", "luxi");
                bundle.putInt("birthday", 1997);
                bundle.putSerializable("student", new Student("王五", 5));
```

1. Bundle传递的数据包括：string、int、boolean、byte、float、long、double等基本类型或它们对应的数组。
2. 当Bundle传递的是对象或对象数组时，必须实现Serialiable或Parcelable接口。
3. Bundle所保存的数据是以key-value(键值对)的形式保存在ArrayMap中

### 3.2 使用文件共享的方式

1. 文件共享对于文件格式没有要求，只要双方约定数据格式即可。
2. 如果存在并发写入的情况，我们应该使用进程同步限制多个进程的写操作。
3. SharedPreferences不能用于共享文件实现进程通信。因为系统对于它的读写有一定的缓存策略，在内存中有SharedPreferences的缓存。在多进程下，系统对他的读写就会变得不可靠。

### 3.3 使用Messenger的方式

#### 3.3.1 Messenger 概述

Messenger是基于消息Message的传递的一种轻量级IPC进程间通信方式（通过在一个进程中创建一个指向Handler的Messenger，并将该Messenger传递给另一个进程），当然本质就是对Binder的封装（也是通过AIDL实现的 ）。通过Messenger可以让我们可以简单地在进程间直接使用Handler进行Message传递，跨进程是通过Binder（AIDL实现），而消息发送是通过Handler#sendMessage方法，而处理则是Handler#handleMessage处理的；当然除了Handler之外还可以是自定义的相关的某些IBinder接口，简而言之，Messenger的跨进程能力是由构造时关联的对象提供的。

#### 3.3.2 Messenger 的具体实现

<div align=center>==============客户端=============</div>

1. 

```java
 private final ServiceConnection mConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            mMessenger = new Messenger(service);
            mBound = true;
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            mMessenger = null;
            mBound = false;
        }
    };
```

2. 准备一个接收服务端消息的Handler

```java
    private static class MainActivityHandle extends Handler {

        public MainActivityHandle(@NonNull Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(@NonNull Message msg) {
            switch (msg.what) {
                case 2:
                    Bundle data = msg.getData();
                    String reply = data.getString("reply");
                    Log.d(TAG, "handleMessage: " + reply);
                    break;
                default:
                    super.handleMessage(msg);
                    break;
            }
        }
    }
```

3. 准备一个保存消息的Messenge（从服务端返回的Messenge）

```java
   @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mGetReplyMessenger = new Messenger(new MainActivityHandle(Looper.getMainLooper()));

    }
```

4. 绑定服务

```java
@Override
protected void onStart() {
    super.onStart();
    Intent intent = new Intent(this, MyService.class);
    bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
}
```

5. 向服务端发送消息。   `obtain.replyTo = mGetReplyMessenger;`将`mGetReplyMessenger`传递给服务端。

```java
private void sendMessage() {
    if (!mBound) {
        return;
    }
    Message obtain = Message.obtain(null, 1);
    Bundle bundle = new Bundle();
    bundle.putString("msg", "这是一条消息");
    obtain.setData(bundle);
    obtain.replyTo = mGetReplyMessenger;
    try {
        mMessenger.send(obtain);
    } catch (RemoteException e) {
        e.printStackTrace();
    }
}
```

<div align=center>==============服务端=============</div>

```java
public class MyService extends Service {

    private static final String TAG = MyService.class.getSimpleName();
    private final Messenger mMessenger;

    public MyService() {
        //创建线程
        HandlerThread handlerThread = new HandlerThread("massage_handle");
        handlerThread.start();
        mMessenger = new Messenger(new MessageHandler(handlerThread.getLooper()));
    }

    private class MessageHandler extends Handler {

        public MessageHandler(@NonNull Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(@NonNull Message msg) {
            switch (msg.what) {
                case 1:
                    Log.d(TAG, "handleMessage: " + msg);
                    Bundle data = msg.getData();
                    String msg1 = data.getString("msg");
                    Log.d(TAG, "handleMessage: " + msg1);
                    replyToClient(msg);
                    break;
                default:
                    super.handleMessage(msg);
                    break;
            }
        }
    }


    private void replyToClient(Message msg) {
        Messenger replyTo = msg.replyTo;
        Message obtain = Message.obtain(null, 2);
        Bundle bundle = new Bundle();
        bundle.putString("reply", "返回的消息!!");
        obtain.setData(bundle);
        try {
            replyTo.send(obtain);
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mMessenger.getBinder();

    }
}
```
