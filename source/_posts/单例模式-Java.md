---
title: 单例模式(Java版)
index_img: https://s2.loli.net/2024/02/18/lXsHVAfxgm1PG9W.webp
top_img: https://img.onlinedown.net/download/202306/174942-649d53b6c0d02.jpg
category:
  - 设计模式
tags:
  - Java
abbrlink: d090
date: 2024-01-24 00:11:37
---









## 1.饿汉式

| 是否 Lazy 初始化 | 否   |
| ---------------- | ---- |
| 是否多线程安全   | 是   |
| 实现难度         | 易   |

- **描述：**这种方式比较常用，但容易产生垃圾对象。
- 优点：没有加锁，执行效率会提高。
- 缺点：类加载时就初始化，浪费内存。

它基于 classloader 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，虽然导致类装载的原因有很多种，在单例模式中大多数都是调用 getInstance 方法， 但是也不能确定有其他的方式（或者其他的静态方法）导致类装载，这时候初始化 instance 显然没有达到 lazy loading 的效果。

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```



## 2. 双检锁/双重校验锁

需要增加 volatile 关键字，禁止指令重排序：

| 是否 Lazy 初始化 | 是     |
| ---------------- | ------ |
| 是否多线程安全   | 是     |
| 实现难度         | 较复杂 |

```java
public class Singleton{
    //volatile关键字
  private static volatile Singleton instance;
  private Singleton(){}
  public static Singleton getInstance(){
    if(instance == null){
    // 判断对象是否以及实例化过，没有则进入加锁代码块，此处可能有多个线程同时进来，等待类对象锁
      synchronized(Singleton.class){
      // 获取类对象锁，其他线程在外等待，其他线程进来再次判断，如果对象实例化了，则不需要再实例化
        if(instance == null){
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}
```

## 3.登记式/静态内部类

| 是否 Lazy 初始化 | 是   |
| ---------------- | ---- |
| 是否多线程安全   | 是   |
| 实现难度         | 一般 |

**描述：**这种方式能达到双检锁方式一样的功效，但实现更简单。对静态域使用延迟初始化，应使用这种方式而不是双检锁方式。这种方式只适用于静态域的情况，双检锁方式可在实例域需要延迟初始化时使用。

这种方式同样利用了 classloader 机制来保证初始化 instance 时只有一个线程，它跟第 1种方式不同的是：第 1 种方式只要 Singleton 类被装载了，那么 instance 就会被实例化（没有达到 lazy loading 效果），而这种方式是 Singleton 类被装载了，instance 不一定被初始化。因为 SingletonHolder 类没有被主动使用，只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类，从而实例化 instance。想象一下，如果实例化 instance 很消耗资源，所以想让它延迟加载，另外一方面，又不希望在 Singleton 类加载时就实例化，因为不能确保 Singleton 类还可能在其他的地方被主动使用从而被加载，那么这个时候实例化 instance 显然是不合适的。这个时候，这种方式相比第 1 种方式就显得很合理。

```java
public class Singleton {  
    private static class SingletonHolder {  
    private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
        return SingletonHolder.INSTANCE;  
    }  
}
```

## 4.枚举

| 是否 Lazy 初始化 | 否   |
| ---------------- | ---- |
| 是否多线程安全   | 是   |
| 实现难度         | 易   |

```java
public enum Singleton {  
    INSTANCE;  
    public void whateverMethod() {  
    }  
}
```

这种方式是 Effective Java 作者 Josh Bloch 提倡的方式，它不仅能避免多线程同步问题，而且还自动支持序列化机制，防止反序列化重新创建新的对象，绝对防止多次实例化。



## 使用场景：

1. 涉及到序列号的时候，可以使用`枚举单例`
2. 明确实现 lazy loading 效果时，才会使用登记式/静态内部类。
3. 正常情况下可以使用`双检锁/双重校验锁`和`饿汉式`。
