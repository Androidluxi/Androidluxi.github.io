---
title: android 源码的编译-1
abbrlink: 114b
index_img: /img/19.jpg
category:
  - Android 进阶
tags:
  - Android
date: 2024-03-19 23:35:07
---

<meta name="referrer" content="no-referrer"/>

我们顺利的将AOSP下载了下来后,很多时候我们不仅仅需要去查看源码,还有以下的几个需求:

1. 定制Android系统
2. 将最新版本的Android系统刷入到自己的Android设备中
3. 将整个系统源码导入到Android Studio中
4. 动态调试Android系统源码 （不一定需要导入全部的源码，部分源码也可以调试）

## 源码编译相关名词：

1. Makefile：

   make命令执行时，需要一个 Makefile 文件，以告诉make命令需要怎么样的去编译和链接程序。

   首先，我们用一个示例来说明Makefile的书写规则。以便给大家一个感兴认识。这个示例来源于GNU的make使用手册，在这个示例中，我们的工程有8个C文件，和3个头文件，我们要写一个Makefile来告诉make命令如何编译和链接这几个文件。我们的规则是：
       1）如果这个工程没有编译过，那么我们的所有C文件都要编译并被链接。
       2）如果这个工程的某几个C文件被修改，那么我们只编译被修改的C文件，并链接目标程序。
       3）如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的C文件，并链接目标程序。

   只要我们的Makefile写得够好，所有的这一切，我们只用一个make命令就可以完成，make命令会自动智能地根据当前的文件修改的情况来确定哪些文件需要重编译，从而自己编译所需要的文件和链接目标程序。

   原文链接：[跟我一起写 Makefile（一）](https://blog.csdn.net/haoel/article/details/2886)

2. Android.mk

   属于Android编译环境下一个特殊的makefile文件，Android.mk中定义了一个模块的必要的参数，使模块跟着平台编译，通俗的讲就是告诉编译系统应该以什么样的规则来编译你的源代码，并生成对应的目标文件。

   [Android.mk解析与使用](https://zhuanlan.zhihu.com/p/680173022)

3. Ninja

   在Android编译环境中，Ninja 是一个快速、小巧且采用无依赖规则的构建系统。相比于传统的 Make 或其他构建工具，Ninja 专注于执行速度，尤其擅长处理大型项目和多线程编译任务。

   在Android源码编译流程中，Ninja 被用来作为最终的构建工具执行阶段。CMake 或其他前端构建工具（例如Google之前使用的Soong）生成 Ninja 构建文件（通常扩展名为 `.ninja`），这些文件包含了编译项目的详细指令和依赖关系图。当工程配置发生更改时，Ninja 可以迅速确定哪些目标需要重新构建，并高效地调度多个编译任务进行并行执行，从而极大地加速了整个编译过程。

4. Soong

   [Soong 构建系统](https://android.googlesource.com/platform/build/soong/+/refs/heads/master/README.md)是在 Android 7.0 (Nougat) 中引入的，旨在取代 Make。它利用 [Kati](https://github.com/google/kati/blob/master/README.md) GNU Make 克隆工具和 [Ninja](https://ninja-build.org/) 构建系统组件来加速 Android 的构建。Soong 使用的是 Go 语言编写，它提供了一种声明式的、模块化的配置和构建框架，使得 Android 源码树的构建过程更为灵活和高效。

5. Blueprint

   Blueprint是Android系统中的一种构建系统，它是Soong的一部分，用于定义和构建Android系统中的各种组件。Blueprint使用一种声明式的方法来定义组件的构建规则，包括组件的依赖关系、编译选项、生成规则等。

   Blueprint是生成、解析Android.bp的工具，是Soong的一部分。翻译成Ninja语法。

   Soong则是专为Android编译而设计的工具，Blueprint只是解析文件的形式，而Soong则解释内容的含义。

6. kati 

   在Android编译系统的历史发展中，Kati 是一个过渡性的构建工具，由 Google 开发，旨在提高 Android 源码树的编译性能。在 Soong 构建系统完全取代旧有的 Makefile 构建流程之前，Kati 起到了关键的作用。

   Kati 主要设计目的是为了加速基于 Makefile 的 Android 编译过程。它通过解析 Makefile 并将其转换为更高效的内部表示形式，然后生成 Ninja 构建文件，利用 Ninja 的高性能特性来进行并行编译，从而提升 Android 源码编译的速度。

   尽管 Kati 曾经在一段时间内被用于优化 Android 编译流程，但随着 Soong 构建系统的成熟和完善，Google 已逐步淘汰 Kati，转而全面采用 Soong 和 Ninja 的组合来构建 Android 系统，以获得更好的构建性能和灵活性。

7. Android.bp

   Android.bp文件首先是Android系统的一种编译配置文件，是用来代替原来的Android.mk文件的

### 概念之间的联系为:

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240320000939.png"/>

### Android.mk 和Android.bp 的区别

Android.mk 是 Makefile 的一种形式，同样采用的是 DSL(Domain Specific Language)，包括不同的规则、条件、分支、循环等复杂的控制等等。详细看另一篇《Makefile (一)之 简介》
Android.bp 是在 android 7.0 之后使用，是一种简单的配置文件，并不像makefile 有条件、分支、循环等控制，也没有运算、逻辑等操作
Android.mk 文件通常具有多个具有相同名称的模块（如：库的静态和共享版本，或主机和设备版本）
Android.bp文件需要每个模块有唯一的名称，但是可以在多个变体中构建单个模块，如通过添加 host_supported: true。androidmk转换器会生成多个冲突的模块，必须通过处理单个模块的target: { android: { }, host: { } }块里的差异来手动解决

原文链接：[Android中的Android.bp、Blueprint 和Soong简介](https://blog.csdn.net/shift_wwx/article/details/84790429)

[理解Android.bp - Gityuan博客 | 袁辉辉的技术博客](https://gityuan.com/2018/06/02/android-bp/)
