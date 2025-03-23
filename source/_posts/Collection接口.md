---
title: Collection接口
date: 2023-04-24 01:00:08
index_img: /img/9.jpg
category:
- Java
tags:
- Java
---

# Collection

因为接口无法创建对象。接口是抽象化的，无法实例化对象。所以下面将使用collection的子类来介绍collection里面的方法。

> 这是多态，父类型的引用指向子类型的对象。

```java
Collection list = new ArrayList<>();
```

| **return**  | method                            | description                                  |
| :---------- | --------------------------------- | -------------------------------------------- |
| iterator< > | iterator()                        | 返回在此 collection 的元素上进行迭代的迭代器 |
| boolean     | add(E o)                          | 向集合中添加元素。                           |
| int         | size()                            | 返回此 collection 中的元素数。               |
| void        | clear()                           | 清空集合                                     |
| void        | isEmpty()                         | 判断集合是否为空，个数是否为0；              |
| boolean     | remov(Object o)                   | 删除集合中的某个元素                         |
| boolean     | contains(Object o)                | 判断集合中是否包含元素o，包含返回true        |
| Objec[ ]    | toArray()                         | 将集合转化成数组                             |
| boolean     | addAll(Collection< ? extend E  >) | 向集合中添加一个集合                         |
| boolean     | retainAll(Collecyion  < ? >  o )  | 两个集合合并，取交集。                       |

开始测试方法：

```java
public class main {
    public static void main(String[] args) {
        Collection<Object> list = new ArrayList<>();
        //自动装箱，集合中只能存储对象的地址，不能存放基本数据类型和对象。
        list.add(212);
        list.add('3');
        list.add(2.454);
        list.add("夸克");
        int a = list.size();
        System.out.println(a);
        boolean b = list.contains("夸克");
        System.out.println(b);
        list.clear();
        System.out.println(list.isEmpty());
        Collection<Object> list2 = new ArrayList<>();
        list2.add("浩克");
        list.add("浩克");
        list.add("夸克");
        list.addAll(list2);
        list.retainAll(list2);
        for (Object o : list) {
            System.out.println(o);
        }
    }
}
```

## 重点：iterator迭代器

迭代器是一个对象，里面的方法有：

| **方法摘要** |             |                                                              |
| ------------ | ----------- | ------------------------------------------------------------ |
| ` boolean`   | hasNext()   | 如果仍有元素可以迭代，则返回 `true`。                        |
| ` E`         | next()      | 返回迭代的下一个元素。                                       |
| ` void`      | remove() 。 | 从迭代器指向的 collection 中移除迭代器返回的最后一个元素（可选操作） |

<img src="Collection接口/004-迭代集合的原理.png" alt="004-迭代集合的原理" style="zoom: 200%;" />

```java
public class main {
    public static void main(String[] args) {
        Collection<Object> list = new HashSet<>();
        //自动装箱，集合中只能存储对象的地址，不能存放基本数据类型和对象。
        list.add(212);
        list.add('3');
        list.add(2.454);
        list.add("夸克");
        Iterator<Object> iterator = list.iterator();
       while (iterator.hasNext()){
            Object a = iterator.next();
            System.out.println(a.toString());

        }
    }
}
```

