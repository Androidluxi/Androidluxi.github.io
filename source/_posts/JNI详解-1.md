---
title: JNI详解
date: 2025-10-21 21:20:36
index_img: /img/1.jpg
category:
  - Android
tags:
  - Android
---

<meta name="referrer" content="no-referrer"/>

# JNI 知识详解

在 Android 生态中主要有 C/C++、Java、Kotlin 三种语言 ，它们的关系不是替换而是互补。其中，C/C++ 的语境是算法和高性能，Java 的语境是平台无关和内存管理，而 Kotlin 则融合了多种语言中的优秀特性，带来了一种更现代化的编程方式。

# 1. 认识 JNI

<img src="https://gitee.com/silent-learner/imgs/raw/master/2025图片/20251021212245766.png"/>

### 1.1 为什么要使用 JNI？

**JNI（Java Native Interface，Java 本地接口）是 Java 生态的特性，它扩展了 Java 虚拟机的能力，使得 Java 代码可以与 C/C++ 代码进行交互。** 通过 JNI 接口，Java 代码可以调用 C/C++ 代码，C/C++ 代码也可以调用 Java 代码。

这就引出第 1 个问题（为什么要这么做）：Java 为什么要调用 C/C++ 代码，而不是直接用 Java 开发需求呢？我认为主要有 4 个原因：

**原因 1 - Java 天然需要 JNI 技术：** 虽然 Java 是平台无关性语言，但运行 Java 语言的虚拟机是运行在具体平台上的，也就是说 Java 虚拟机是平台相关的。因此，在调用平台 API 的功能时，虽然在 Java 语言层是平台无关的，但背后只能通过 JNI 技术在 Native 层分别调用不同平台 API（例如打开文件功能，在 Window 平台是 openFile 函数，而在 Linux 平台是 open 函数）。类似的，对于有操作硬件需求的程序，也只能通过 C/C++ 实现对硬件的操作，再通过 JNI 调用；

**原因 2 - Java 运行效率不及 C/C++：** Java 代码的运行效率相对于 C/C++ 要低一些，因此，对于有密集计算（例如实时渲染、音视频处理、游戏引擎等）需求的程序，会选择用 C/C++ 实现，再通过 JNI 调用；

**原因 3 - Native 层代码安全性更高：** 反编译 so 文件的难度比反编译 Class 文件高，一些跟密码相关的功能会选择用 C/C++ 实现，再通过 JNI 调用；

**原因 4 - 复用现有代码：** 当 C/C++ 存在程序需要的功能时，则可以直接复用。

还有第 2 个问题（为什么可以这么做）：为什么两种独立的语言可以实现交互呢？因为 Java 虚拟机本身就是 C/C++ 实现的，无论是 Java 代码还是 C/C++ 代码，最终都是由这个虚拟机支撑，共同使用一个进程空间。JNI 要做的只是在两种语言之间做桥接。

### 1.2 JNI 开发的基本流程

一个标准的 JNI 开发流程主要包含以下步骤：

- 1、创建 `HelloWorld.java`，并声明 native 方法 sayHi()；
- 2、使用 javac 命令编译源文件，生成 `HelloWorld.class` 字节码文件；
- 3、使用 javah 命令导出 `HelloWorld.h` 头文件（头文件中包含了本地方法的函数原型）；
- 4、在源文件 `HelloWorld.cpp` 中实现函数原型；
- 5、编译本地代码，生成 `Hello-World.so` 动态原生库文件；
- 6、在 Java 代码中调用 System.loadLibrary(...) 加载 so 文件；
- 7、使用 Java 命令运行 HelloWorld 程序。

### 1.3 JNI 的性能误区

JNI 本身本身并不能解决性能问题，错误地使用 JNI 反而可能引入新的性能问题，这些问题都是要注意的：

- **问题 1 - 跨越 JNI 边界的调用：** 从 Java 调用 Native 或从 Native 调用 Java 的成本很高，使用 JNI 时要限制跨越 JNI 边界的调用次数；
- **问题 2 - 引用类型数据的回收：** 由于引用类型数据（例如字符串、数组）传递到 JNI 层的只是一个指针，为避免该对象被垃圾回收虚拟机会固定住（pin）对象，在 JNI 方法返回前会阻止其垃圾回收。因此，要尽量缩短 JNI 调用的执行时间，它能够缩短对象被固定的时间（关于引用类型数据的处理，在下文会说到）。

### 1.4 注册 JNI 函数的方式

Java 的 native 方法和 JNI 函数是一一对应的映射关系，建立这种映射关系的注册方式有 2 种：

- **方式 1 - 静态注册：** 基于命名约定建立映射关系；
- **方式 2 - 动态注册：** 通过 `JNINativeMethod` 结构体建立映射关系

### 1.5 加载 so 库的时机

so 库需要在运行时调用 `System.loadLibrary(…)` 加载，一般有 2 种调用时机：

- **1、在类静态初始化中：** 如果只在一个类或者很少类中使用到该 so 库，则最常见的方式是在类的静态初始化块中调用；
- **2、在 Application 初始化时调用：** 如果有很多类需要使用到该 so 库，则可以考虑在 Application 初始化等场景中提前加载。

# 2. JNI 模板代码

为什么 JNI 函数名要采用  `Java_com_xurui_HelloWorld_sayHi` 的命名方式呢？—— 这是 JNI 函数静态注册约定的函数命名规则。Java 的 native 方法和 JNI 函数是一一对应的映射关系，而建立这种映射关系的注册方式有 2 种：静态注册 + 动态注册。

其中，静态注册是基于命名约定建立的映射关系，一个 Java 的 native 方法对应的 JNI 函数会采用约定的函数名，即 `Java_[类的全限定名 (带下划线)]_[方法名]` 。JNI 调用 `sayHi()` 方法时，就会从 JNI 函数库中寻找函数 `Java_com_xurui_HelloWorld_sayHi()`，

### 2.2 关键词 JNIEXPORT

`JNIEXPORT` 是宏定义，表示一个函数需要暴露给共享库外部使用时。

### 2.3 关键词 JNICALL

`JNICALL` 是宏定义，表示一个函数是 JNI 函数。

### 2.4 参数 jobject

`jobject` 类型是 JNI 层对于 Java 层应用类型对象的表示。每一个从 Java 调用的 native 方法，在 JNI 函数中都会传递一个当前对象的引用。区分 2 种情况：

- **1、静态 native 方法：** 第二个参数为 `jclass` 类型，指向 native 方法所在类的 Class 对象；
- **2、实例 native 方法：** 第二个参数为 `jobject` 类型，指向调用 native 方法的对象。

### 2.5 JavaVM 和 JNIEnv 的作用

`JavaVM` 和 `JNIEnv` 是定义在 jni.h 头文件中最关键的两个数据结构：

- **JavaVM：** 代表 Java 虚拟机，每个 Java 进程有且仅有一个全局的 JavaVM 对象，JavaVM 可以跨线程共享；
- **JNIEnv：** 代表 Java 运行环境，每个 Java 线程都有各自独立的 JNIEnv 对象，JNIEnv 不可以跨线程共享。

**JavaVM 和 JNIEnv 的类型定义在 C 和 C++ 中略有不同，但本质上是相同的，内部由一系列指向虚拟机内部的函数指针组成。** 类似于 Java 中的 Interface 概念，不同的虚拟机实现会从它们派生出不同的实现类，而向 JNI 层屏蔽了虚拟机内部实现（例如在 Android ART 虚拟机中，它们的实现分别是 JavaVMExt 和 JNIEnvExt）。

<img src="https://gitee.com/silent-learner/imgs/raw/master/2025图片/20251021212249405.png"/>

# 3. 数据类型转换

### 3.1 Java 类型映射（重点理解）

JNI 对于 Java 的基础数据类型（int 等）和引用数据类型（Object、Class、数组等）的处理方式不同。这个原理非常重要，理解这个原理才能理解后面所有 JNI 函数的设计思路：

- **基础数据类型：** 会直接转换为 C/C++ 的基础数据类型，例如 int 类型映射为 jint 类型。由于 jint 是 C/C++ 类型，所以可以直接当作普通 C/C++ 变量使用，而不需要依赖 JNIEnv 环境对象；
- **引用数据类型：** 对象只会转换为一个 C/C++ 指针，例如 Object 类型映射为 jobject 类型。由于指针指向 Java 虚拟机内部的数据结构，所以不可能直接在 C/C++ 代码中操作对象，而是需要依赖 JNIEnv 环境对象。另外，为了避免对象在使用时突然被回收，在本地方法返回前，虚拟机会固定（pin）对象，阻止其 GC。

另外需要特别注意一点，基础数据类型在映射时是直接映射，而不会发生数据格式转换。例如，Java `char` 类型在映射为 `jchar` 后旧是保持 Java 层的样子，数据长度依旧是 2 个字节，而字符编码依旧是 UNT-16 编码。

| Java 类型  | JNI 类型             | 描述          | 长度（字节） |
| ---------- | -------------------- | ------------- | ------------ |
| boolean    | jboolean             | unsigned char | 1            |
| byte jbyte | signed char          | 1             |              |
| char       | jchar                | short         | 2            |
| short      | jshort               | signed short  |              |
| int        | jint、jsize          | int 4         |              |
| long       | jlong                | signed long   | 8            |
| float      | float                | signed float  | 4            |
| double     | jdouble              | signed double | 8            |
| Class      | jclass               | Class 类对象  | 1            |
| String     | jstring              | 字符串对象    | /            |
| Object     | jobject              | 对象          | /            |
| Throwable  | throwable            | 异常对象      | /            |
| boolean[]  | jbooleanArray        | 布尔数组      | /            |
| byte[]     | jbyteArray byte 数组 | /             |              |
| char[]     | jArray               | char 数组     | /            |
| short[]    | jshortArray          | short 数组    | /            |
| int[]      | jintArray            | int 数组      | /            |
| long[]     | jlongArray           | long 数组     | /            |
| float[]    | jfloatArray          | float 数组    | /            |
| double[]   | jdoubleArray         | double 数组   | /            |

### 3.2 字符串类型操作

上面提到 Java 对象会映射为一个 jobject 指针，那么 Java 中的 java.lang.String 字符串类型也会映射为一个 jobject 指针。可能是因为字符串的使用频率实在是太高了，所以 JNI 规范还专门定义了一个 jobject 的派生类 `jstring` 来表示 Java String 类型，这个相对特殊。

由于 Java 与 C/C++ 默认使用不同的字符编码，因此在操作字符数据时，需要特别注意在 UTF-16 和 UTF-8 两种编码之间转换。

- **Unicode：** 统一化字符编码标准，为全世界所有字符定义统一的码点，例如 U+0011；
- **UTF-8：** Unicode 标准的实现编码之一，使用 1~4 字节的变长编码。UTF-8 编码中的一字节编码与 ASCII 编码兼容。
- **UTF-16：** Unicode 标准的实现编码之一，使用 2 / 4 字节的变长编码。UTF-16 是 Java String 使用的字符编码；
- **UTF-32：** Unicode 标准的实现编码之一，使用 4 字节定长编码。

以下为 2 种较为常见的转换场景：

- **1、Java String 对象转换为 C/C++ 字符串：** 调用 `GetStringUTFChars` 函数将一个 jstring 指针转换为一个 UTF-8 的 C/C++ 字符串，并在不再使用时调用 `ReleaseStringChars` 函数释放内存；
- **2、构造 Java String 对象：** 调用 `NewStringUTF` 函数构造一个新的 Java String 字符串对象
