---
title: RemoteCallbackList分析
date: 2024-10-31 01:28:31
index_img: /img/siteicon/RemoteCallbackList分析.png
category:
  - Android
  - IPC
tags:
  - Android
abbrlink: 'sdax'
---



对于`RemoteCallbackList`的使用，本博客不再介绍。网上很多博客都有相关的描述。本文主要是对我们为什么要这样或者那样去使用`RemoteCallbackList`做一个分析，学习一下源码时怎么封装的

首先带着几个问题去学习会更加深刻：

1. 为什么`beginBroadcast`和`finishBroadcast`要成对使用？
2. 为什么我们使用`RemoteCallbackList`中的`unregister`能移除监听，我们自己写的普通的ArrayList存放监听回调后，移除没有效果？
3. 怎么更好的使用`RemoteCallbackList`，有哪些需要注意的地方吗？

## 问题 1

为什么我们使用`RemoteCallbackList`中的`unregister`能移除监听，我们自己写的普通的ArrayList存放监听回调后，移除没有效果，就需要去看`RemoteCallbackList`是怎么管理监听回调的

首先看一下里面定义的全局变量：

```java
ArrayMap<IBinder, Callback> mCallbacks = new ArrayMap<IBinder, Callback>();//来维护每个IBinder与Callback的对应,
private Object[] mActiveBroadcast;//一个数组对象？
private int mBroadcastCount = -1;//广播数量？   
private boolean mKilled = false;
private StringBuilder mRecentCallers;
```

下面就是我们使用的`register`方法,可以选择传一或两个参数。

```java
public boolean register(E callback) {
    return register(callback, null);
}

public boolean register(E callback, Object cookie) {
    synchronized (mCallbacks) {
        if (mKilled) {
            return false;
        }
        // Flag unusual case that could be caused by a leak. b/36778087
        logExcessiveCallbacks();
        IBinder binder = callback.asBinder();//取到了Binder对象
        try {
            Callback cb = new Callback(callback, cookie);//将传入的信息封装进Callback对象
            unregister(callback);//避免重复注册
            binder.linkToDeath(cb, 0);//注册一个死亡监听器
            mCallbacks.put(binder, cb);//将我们封装的Callback对象和binder存放进ArrayMap
            return true;
        } catch (RemoteException e) {
            return false;
        }
    }
}
```

这里一个需要关注的点是`Callback`类，下面的注释很清晰

```java
    private final class Callback implements IBinder.DeathRecipient {
        final E mCallback;//回调
        final Object mCookie;//回调的关联信息

        Callback(E callback, Object cookie) {
            mCallback = callback;
            mCookie = cookie;
        }
        //实现了binderDied方法，当binder死亡时，会ArratMap中移除mCallback.asBinder()对应的键值对。
        public void binderDied() {
            synchronized (mCallbacks) {
                mCallbacks.remove(mCallback.asBinder());
            }
            onCallbackDied(mCallback, mCookie);//当我们继承RemoteCallbackList时，可以重写这个方法，对死亡的监听进行处理
        }
    }
```

`unregister`的实现逻辑：

```java
public boolean unregister(E callback) {
    synchronized (mCallbacks) {
        Callback cb = mCallbacks.remove(callback.asBinder());//ArratMap中移除mCallback.asBinder()对应的键值对
        if (cb != null) {
            cb.mCallback.asBinder().unlinkToDeath(cb, 0);//解除Binder死亡监听
            return true;
        }
        return false;
    }

```

在客户端调用unregister方法，如果我们是使用普通的`ArrayList`存放，大概率会使用`list.remove(listener)`,当然这种方式并不能移除监听，思考一个问题：客户端传过来的listener还是原来的listener对象吗？IPC通信是有一个序列化的过程的，序列化前和序列化后的对象并不是同一个对象。

那么`RemoteCallbackList`是怎么解决这个问题的呢？是Binder对象。`IBinder binder = callback.asBinder();`虽然回调对象都是新生成的，但是他们底层的binder对象还是同一个，利用这个特性，`RemoteCallbackList`再自身维护的ArrayMap中移除我们指定的对象。

当然可以看到存放进ArrayMap中的每一个对象里都注册了死亡监听，当Binder失效的时候，会帮我们自动移除listener。

## 问题 2

为什么`beginBroadcast`和`finishBroadcast`要成对使用？

```java
    public int beginBroadcast() {
        synchronized (mCallbacks) {
            // 检查是否已经在广播过程中，如果是，抛出异常
            if (mBroadcastCount > 0) {
                throw new IllegalStateException(
                        "beginBroadcast() called while already in a broadcast");
            }
            // 设置当前广播计数，并获取回调对象的数量
            final int N = mBroadcastCount = mCallbacks.size();
            if (N <= 0) {
                return 0;
            }
            // 检查并更新当前激活的广播数组
            Object[] active = mActiveBroadcast;
            if (active == null || active.length < N) {
                mActiveBroadcast = active = new Object[N];
            }
            for (int i=0; i<N; i++) {
                active[i] = mCallbacks.valueAt(i);
            }
            return N;
      
```

可以看到在开始广播后，将所有的回调都存放进了一个数组，我们最后是去取数组中的listener对象进行通知，这样的原因是：

数组的访问速度比 ArrayMap 更快，特别是在频繁访问的情况下。这里通过将 ArrayMap 中的值复制到数组中，可以减少在广播过程中对 ArrayMap 的多次访问，提高性能。

其次在我们开始广播后，就确定了要通知的listener对象，避免在多线程环境下对 ArrayMap 的并发修改问题。

```java
public void finishBroadcast() {
    synchronized (mCallbacks) {
        if (mBroadcastCount < 0) {
            throw new IllegalStateException(
                    "finishBroadcast() called outside of a broadcast");
        }
        Object[] active = mActiveBroadcast;
        if (active != null) {
            final int N = mBroadcastCount;
            for (int i=0; i<N; i++) {
                active[i] = null;//重置
            }
        }
        mBroadcastCount = -1;//重置
    }
}
```

看了上面的源码，也就明白为什么要成对使用`beginBroadcast`和`finishBroadcast`了。为了确保线程安全，当确定要IPC通信时，会把要通知的对象放到数组里面，那么当我们通信结束，自然也得清空数组里面的数据。

## 问题 3

怎么更好的使用`RemoteCallbackList`，有哪些需要注意的地方吗？

总结以下几点：

1. 可以继承`RemoteCallbackList`，比如实现`onCallbackDied`方法，处理Binder死亡情况进一步处理。
2. 一次只能有一对`beginBroadcast`和`finishBroadcast`，所以要避免通知调用，或者加锁
3. register(E callback, Object cookie) 可以携带一些关键信息，比如客户端的包名，方便我们定位
