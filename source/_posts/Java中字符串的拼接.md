---
title: Java中字符串的拼接
date: 2023-04-19 02:49:01
index_img: /img/25.jpg
category:
- Java知识
tags:
- Java
---

Java中字符串一旦创建，就是不可变的。

# 一、“+” 操作符

“+” 操作符是字符串拼接最常用的方法之一。

使用“+”，字符串的发生拼接时候，会创建一个新的字符串，如果发生大量的字符串的拼接，就会在方法区里面的字符串常量池内不断的出现新的字符串。导致内存大量的浪费。给Java的方法区常量池带来很大的压力。

# 二、StringBuffer

构造一个其中不带字符的字符串缓冲区，其初始容量为 16 个字符。

```java
StringBuffer stringBuffer = new StringBuffer();
```

底层是一个char类型的数组。如果char类型的数组满了，

```
AbstractStringBuilder(int capacity) {
    value = new char[capacity];
}
```

append方法是追加的意思，如果char类型的数组满了，append方法会给数组扩容。

简要分析一下append的底层逻辑：

1. 可以看到append方法，这个是追加String类型的字符。方法重载，对应的还有int、float、long、boolean等类型的append方法。

```java
@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

2. append方法调用的是父类的方法。我们可以看到方法体里面有一个ensureCapasityInternal方法——确保内部容量。在它的底层就有数组扩容的逻辑。

```java
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value, count);
    count += len;
    return this;
}
```

此外，我们可以看到str==null时候，这时候会给字符串追加一个null的字符串。如下：![image-20230419021203366](Java中字符串的拼接/image-20230419021203366.png)

将这个字符串中的字符复制到目标字符数组中： str.getChars(0, len, value, count);

3. 如果我们研究ensureCapasityInternal的底层逻辑，会发现它是通过数组复制实现的。

```java
public static char[] copyOf(char[] original, int newLength) {
    char[] copy = new char[newLength];
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

总之，append方法当追加的字符超过数组容量时，会自动扩容。

### 如何优化StringBuffer的性能？

在创建StringBuffer时候尽可能的给定一个初始化容量，最好减少底层数组的扩容次数，与估计一下，给出一个合适的数组容量。

可以使用

```java
StringBuffer stringBuffer = new StringBuffer(44);
```



# 扩展：

为什么String字符串，使用“+”会创建一个新的字符串，而不是使用之前的字符串？

String底层也是一个char数组，但是使用了fina修饰。

```java
/** The value is used for character storage. */
private final char value[];
```

但是StringBuffer底层的char数组没有使用final修饰。

```
/**
 * The value is used for character storage.
 */
char[] value;
```



[StringBuffer的相关方法](https://www.5axxw.com/tools/api/jdk_cn_6.html)

# 三、StringBuilder

使用和StringBuffer类似，里面的方法也类似。

区别：  StringBuilder的方法线程不安全；

​            StringBuffer的方法都有synchronized关键字修饰，是线程安全的。
