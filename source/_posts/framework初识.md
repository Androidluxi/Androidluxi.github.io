---
title: framework初识
index_img: /img/siteicon/Framework初识.png
category:
  - Android 进阶
tags:
  - Android
abbrlink: 3f36
date: 2024-02-29 21:46:04
---



<meta name="referrer" content="no-referrer"/>

# Framework初识

什么是 `Android Framework`开发？

 Android Framework开发是指针对Android操作系统框架层进行的软件开发工作。这一层次是Android平台的核心部分，它为应用程序开发者提供了APIs和系统服务，并定义了应用程序如何与系统交互的标准机制。

在Android Framework开发中，开发者可能涉及的工作内容包括但不限于：

1. **系统服务开发**：设计和实现Android系统中的关键服务组件，如Activity Manager Service、Window Manager Service、Package Manager Service等，这些服务对上层应用提供支持，确保整个系统的正常运行。
2. **API设计与实现**：创建新的API接口或者扩展现有的API，以便于应用程序开发者能够更加方便地访问和控制设备资源，比如添加新的权限控制、硬件驱动接口调用或功能模块。
3. **系统组件定制**：根据需求修改或自定义Android的内置组件，例如Activity、Service、Broadcast Receiver、Content Provider等，以适应特定设备或业务场景的需求。
4. **系统性能优化**：对Framework层的代码进行性能分析和优化，减少内存消耗、提高响应速度，提升整体用户体验。
5. **兼容性与稳定性改进**：处理不同版本间以及不同设备间的兼容性问题，保证框架层代码的稳定性和健壮性。
6. **安全机制强化**：设计和实施安全策略，加强权限管理、数据加密、漏洞修复等方面的开发工作，保障用户隐私和系统安全性。

## 1、Android系统架构

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240229215348.png"/>

### 1.1 内核层（kernel）

它的主要功能和职责包括：

1. **硬件抽象**：Linux Kernel为上层系统提供了统一的硬件访问接口，通过驱动程序实现了对处理器、内存、I/O设备等硬件资源的管理和控制。这使得Android无需关心具体的硬件细节，可以跨不同类型的硬件平台运行。
2. **进程管理**：负责创建和销毁进程，调度CPU时间片给各个进程，实现多任务处理环境。
3. **内存管理**：内核负责整个系统的内存分配与回收，维护虚拟内存空间，支持交换空间以扩大可用内存，并确保进程间的内存隔离。
4. **文件系统支持**：提供多种文件系统支持，如ext4、F2FS等，用于数据存储和交换。
5. **网络通信**：实现TCP/IP协议栈，以及对各种网络硬件的支持，如WiFi、蓝牙、移动网络等。
6. **安全机制**：内核提供了权限控制、SELinux强制访问控制等安全机制，保证系统及应用的安全性。
7. **电源管理**：针对移动设备特性，内核还承担了电池电量监控、设备休眠唤醒策略等电源管理功能。
8. **设备驱动**：为不同的硬件设备编写并加载相应的驱动程序，这些驱动遵循特定的接口规范与上层进行交互，确保硬件设备能够正常工作。

### 1.2 硬件抽象层（HAL层）

 Android的硬件驱动与Linux不同，传统的Liunx内核驱动完全 存在于内核空间中。但是Android在内核外部增加了一个硬件抽象 层(HAL-Hardware Abstraction Layer)，把一部分硬件驱动放到了 HAL层。

为什么存在HAL层？

Linux内核采用了GPL协议，如果硬件厂商需要支持Linux系统，就需要遵照GPL协议公开硬件驱动的源代码，这势必会影响到硬件厂家的核心利益。 Android的HAL层运行在用户空间，HAL是一个“空壳”，Android会 根据不同的需要，加载不同的动态库。这些动态库由硬件厂家提供。硬件厂家把相关硬件功能写入动态库，内核中只开放一些基本的读写接口操作。这样一些硬件厂家的驱动功能就由内核空间移动到了用户空间。 Android的HAL层遵循Apache协议，并不要求它的配套程序，因此厂家提供的驱动库不需要进行开放，保护了硬件厂家的核心利益。

HAL层存在的好处:

1. **硬件无关性**：
   - HAL的主要目标是提供一个与具体硬件平台无关的接口层。通过HAL，上层的Android系统和应用程序可以调用统一的标准接口来访问底层硬件功能，而无需关心这些功能是如何在不同厂商或不同型号的硬件平台上实现的。
2. **兼容性和可移植性**：
   - 由于Android设备种类繁多，不同的硬件配置差异很大，HAL的存在使得Android能够运行在各种硬件平台上，且不需要针对每一种硬件编写特定的驱动程序。这极大地提高了系统的兼容性和可移植性。
3. **模块化和解耦**：
   - HAL将硬件访问逻辑封装成独立的模块，每个模块负责特定的硬件功能，如相机、传感器、音频等。这种模块化设计允许硬件供应商为特定的硬件功能提供定制化的实现，同时不影响Android框架的整体结构和稳定性。
4. **安全增强**：
   - HAL还可以作为隔离内核空间和用户空间之间通信的安全边界，通过HAL接口进行硬件访问可以更好地控制权限和执行安全策略。
5. **开发便利性**：
   - 对于应用开发者来说，他们可以通过Android SDK提供的API直接调用HAL层的接口，从而更容易地开发出兼容多种硬件平台的应用程序，而无需处理复杂的硬件驱动细节。

### 1.3 native层（也称为C/C++层）

指那些使用C和/或C++编写的、运行在Linux内核之上的底层代码部分。这部分代码直接与硬件交互，提供了对操作系统核心功能的访问，以及优化过的高性能服务。

主要包括：系统Native库 和 Android运行时环境

### 1.4 Java框架层

Android的Java框架层是整个Android应用开发架构中的核心部分，它主要负责构建和运行基于Java的应用程序，并且提供了丰富的API来实现各种功能。以下是Java框架层的主要作用：

1. **应用程序组件**：Java框架层定义了四大基本组件——Activity、Service、BroadcastReceiver和ContentProvider。这些组件构成了Android应用程序的基本结构，分别对应用户界面交互、后台服务、系统广播事件处理以及数据存储和共享等功能。
2. **用户界面**：通过使用Android SDK提供的View体系（包括布局XML文件与Java代码），开发者可以方便地构建和定制各种复杂的用户界面，如列表、按钮、文本框等，并进行触摸事件和其他UI事件的响应。
3. **资源管理**：框架层提供了一套完整的资源管理机制，允许开发者以统一的方式访问和操作字符串、图片、音频、视频等各种资源，支持多语言和屏幕适配。
4. **系统服务**：框架层集成了大量的系统服务，如WIFI服务、蓝牙服务、位置服务、通知服务等，通过接口暴露给上层应用调用，从而让开发者能够利用手机的各种硬件能力。
5. **内容提供者(Content Provider)**：提供跨应用的数据共享机制，使得不同应用程序之间可以通过标准的URI方式进行数据读写和交换。
6. **Intent系统**：通过Intent机制，实现了组件间的通信和启动，使得应用程序能够在不同的组件间传递数据和执行请求。
7. **安全性**：Java框架层还包含了一系列安全相关的API和策略，用于权限控制、签名验证、沙盒隔离等，确保了系统的安全性和隐私保护。
8. **兼容性支持库**：为了支持旧版本设备上的新功能，Android提供了兼容性支持库，使得开发者可以在保持向后兼容的同时使用最新的API特性。

`因为Native层和Java框架层的实现代码不同，并不能直接调用native层的接口，就需要一种承接转合的工具，那就是  JNI`

### 1.5 应用层

该层中包含所有的Android应用程序，包括电话、相机、日历等， 我们自己开发的Android应用程序也被安装在这层；大部分的应用 使用JAVA开发，现在Google也开始力推kotlin进行开发

官方架构文档：[平台架构  | Platform  | Android Developers (google.cn)](https://developer.android.google.cn/guide/platform?hl=zh-cn)

大佬博客：[Android 操作系统架构开篇 - Gityuan博客 | 袁辉辉的技术博客](http://gityuan.com/android/)

{% note success %} 

 一般来说：Framework开发包括：Java Framework + JNI +Native 。

{% endnote %}

## 2、Android 启动流程

![](http://gityuan.com/images/android-arch/android-boot.jpg)

Android 系统启动流程：

1.  手机开机后，引导芯片启动，引导芯片开始从固化在 ROM里的预设代码执行，加载引导程序到到RAM，bootloader检 查RAM，初始化硬件参数等功能； 
2. 硬件等参数初始化完成后，进入到Kernel层，Kernel层 主要加载一些硬件设备驱动，初始化进程管理等操作。在Kernel 中首先启动swapper进程（pid=0），用于初始化进程管理、内管 管理、加载Driver等操作，再启动kthread进程(pid=2),这些linux 系统的内核进程，kthread是所有内核进程的鼻祖；  
3. Kernel层加载完毕后，硬件设备驱动与HAL层进行交互。 初始化进程管理等操作会启动 init 进程 ，这些在Native层中；  
4. init进程 (pid=1，init进程是所有进程的鼻祖，第一个启动) 启动后，会启动adbd，logd等用户守护进程，并且会启动 servicemanager(binder服务管家)等重要服务，同时孵化出 zygote进程，这里属于C++ Framework，代码为C++程序；
5. zygote进程是由init进程解析init.rc文件后fork生成，它会加载虚拟机，启动System Server(zygote孵化的第一个进程)； System Server负责启动和管理整个Java Framework，包含 ActivityManager，WindowManager，PackageManager， PowerManager等服务；  
6. zygote同时会启动相关的APP进程，它启动的第一个APP 进程为Launcher，然后启动Email，SMS等进程，所有的APP进程 都有zygote fork生成

### 从进程的角度分析架构

关于Loader层：

- Boot ROM: 当手机处于关机状态时，长按Power键开机，引导芯片开始从固化在`ROM`里的预设代码开始执行，然后加载引导程序到`RAM`；
- Boot Loader：这是启动Android系统之前的引导程序，主要是检查RAM，初始化硬件参数等功能。

#### 2.1 Linux内核层

Android平台的基础是Linux内核，比如ART虚拟机最终调用底层Linux内核来执行功能。Linux内核的安全机制为Android提供相应的保障，也允许设备制造商为内核开发硬件驱动程序。

- 启动Kernel的swapper进程(pid=0)：该进程又称为idle进程, 系统初始化过程Kernel由无到有开创的第一个进程, 用于初始化进程管理、内存管理，加载Display,Camera Driver，Binder Driver等相关工作；
- 启动kthreadd进程（pid=2）：是Linux系统的内核进程，会创建内核工作线程kworkder，软中断线程ksoftirqd，thermal等内核守护进程。`kthreadd进程是所有内核进程的鼻祖`。

#### 2.2 硬件抽象层 (HAL)

硬件抽象层 (HAL) 提供标准接口，HAL包含多个库模块，其中每个模块都为特定类型的硬件组件实现一组接口，比如WIFI/蓝牙模块，当框架API请求访问设备硬件时，Android系统将为该硬件加载相应的库模块。

#### 2.3 Android Runtime & 系统库

每个应用都在其自己的进程中运行，都有自己的虚拟机实例。ART通过执行DEX文件可在设备运行多个虚拟机，DEX文件是一种专为Android设计的字节码格式文件，经过优化，使用内存很少。ART主要功能包括：预先(AOT)和即时(JIT)编译，优化的垃圾回收(GC)，以及调试相关的支持。

这里的Native系统库主要包括init孵化来的用户空间的守护进程、HAL层以及开机动画等。启动init进程(pid=1),是Linux系统的用户进程，`init进程是所有用户进程的鼻祖`。

- init进程会孵化出ueventd、logd、healthd、installd、adbd、lmkd等用户守护进程；
- init进程还启动`servicemanager`(binder服务管家)、`bootanim`(开机动画)等重要服务
- init进程孵化出Zygote进程，Zygote进程是Android系统的第一个Java进程(即虚拟机进程)，`Zygote是所有Java进程的父进程`，Zygote进程本身是由init进程孵化而来的。

#### 2.4 Framework层

- Zygote进程，是由init进程通过解析init.rc文件后fork生成的，Zygote进程主要包含：
  - 加载ZygoteInit类，注册Zygote Socket服务端套接字
  - 加载虚拟机
  - 提前加载类preloadClasses
  - 提前加载资源preloadResouces
- System Server进程，是由Zygote进程fork而来，`System Server是Zygote孵化的第一个进程`，System Server负责启动和管理整个Java framework，包含ActivityManager，WindowManager，PackageManager，PowerManager等服务。
- Media Server进程，是由init进程fork而来，负责启动和管理整个C++ framework，包含AudioFlinger，Camera Service等服务。

#### 2.5 App层

- Zygote进程孵化出的第一个App进程是Launcher，这是用户看到的桌面App；
- Zygote进程还会创建Browser，Phone，Email等App进程，每个App至少运行在一个进程上。
- 所有的App进程都是由Zygote进程fork生成的。

#### 2.6 Syscall && JNI

- Native与Kernel之间有一层系统调用(SysCall)层，见[Linux系统调用(Syscall)原理](http://gityuan.com/2016/05/21/syscall/);
- Java层与Native(C/C++)层之间的纽带JNI，见[Android JNI原理分析](http://gityuan.com/2016/05/28/android-jni/)。
