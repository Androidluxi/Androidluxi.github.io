---
title: ffmpeg交叉编译
date: 2025-05-08 21:45:11
index_img: /img/14.jpg
category:
- Android
tags:
- Android
---

<meta name="referrer" content="no-referrer"/>

## 什么是交叉编译？

‌**交叉编译**‌是指在一种平台上编译程序，使其能够在另一种不同的平台上运行的过程。这种编译方式主要用于开发嵌入式系统、移动设备和其他受限环境中的应用程序‌。

### 交叉编译的基本概念

1. ‌**本地编译**‌：在当前的平台上编译程序，生成的代码直接在当前平台上运行。例如，在x86架构的电脑上编译的程序直接在x86架构的电脑上运行‌。
2. ‌**交叉编译**‌：在一种平台上编译程序，生成的代码在另一种平台上运行。例如，在x86架构的电脑上编译的程序在ARM架构的设备上运行‌。

[交叉编译百度百科](https://baike.baidu.com/item/%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91/10916911)

### 交叉编译的平台

在讨论交叉编译时，常见的平台通常指的是不同的硬件架构和操作系统组合。一般会是

**x86/x86_64 (Intel/AMD Architecture)**

1. 这是桌面和服务器领域最常用的架构。

2. 支持的操作系统包括Windows、Linux、macOS等。

 **ARM (Advanced RISC Machine)**

1. 广泛应用于移动设备如智能手机和平板电脑。
2. ARM架构有多种版本，如ARMv7（用于32位处理器）、ARMv8/Aarch64（用于64位处理器）。

这里我们主要关注在Android平台上的交叉编译，也就是**ARM**硬件平台，支持的子架构包括`armeabi-v7a`（32位）和`arm64-v8a`（64位）。**x86**: 主要用于一些平板电脑和模拟器中。**x86_64**: 类似于x86，但支持64位架构。

## 交叉编译的准备工作

要进行交叉编译，首先需要准备交叉编译工具链。

**交叉编译工具链**是一组用于在一台机器（宿主机）上生成另一台不同架构或操作系统的机器（目标机）可执行文件的程序。这些工具通常包括编译器、汇编器、链接器和调试器等，它们都是针对特定的目标平台进行定制的。

在Android开发中，交叉编译工具链主要通过**Android NDK (Native Development Kit)** 提供。NDK允许开发者使用C或C++编写部分应用，并将其编译为适用于不同Android设备的原生代码。

NDK可以直接在Android Studio中Android Studio SDK Manager下载，也可以在[官网](https://developer.android.google.cn/ndk/downloads/?hl=zh-cn)中下载，下载后解压到相应目录即可。

Android中的**编译器 (Compiler)**通常为GCC或者Clang，前者成熟度高，支持广泛，后者错误提示友好且性能较好。

但是在实际项目中，直接使用编译器命令进行编译往往不够灵活和高效，尤其是在处理大型项目时。这就引入了构建系统工具，比如`Makefile`以及更现代的`CMake`等，它们用于自动化和管理整个构建过程。

<img src="https://gitee.com/silent-learner/imgs/raw/master/2025图片/20250508214545511.png"/>

### `Makefile`与编译器的关系

- **`Makefile`**：是一种用于存储项目构建指令的特殊格式文件。它定义了一系列规则来告诉`make`工具如何编译和链接程序。通过编写`Makefile`，你可以指定哪些文件需要被编译、如何编译这些文件、以及它们之间如何相互依赖。这样做的好处是可以避免每次都手动输入长长的编译命令，并且只重新编译那些真正修改过的文件，从而节省时间。

  使用`make`配合`Makefile`工作时，实际上是调用了底层的编译器（如GCC或Clang）来执行具体的编译任务。

### `CMake`的角色及其与编译器的关系

- **`CMake`**：是一个跨平台的开源构建系统生成器。不同于直接编写`Makefile`或使用`nmake`，`CMake`允许开发者通过编写高层级的配置脚本（`CMakeLists.txt`），然后根据不同的操作系统和编译器自动生成合适的本地构建文件（如`Makefile`、Visual Studio解决方案文件等）。这意味着无论你的目标平台是Linux、Windows还是macOS，都可以使用相同的`CMakeLists.txt`文件来管理和构建项目。
  - **与编译器的关系**：`CMake`本身并不执行编译；相反，它生成适合特定构建系统的配置文件（例如`Makefile`），然后调用相应的构建工具（如`make`或`nmake`）来完成编译过程。在这个过程中，`CMake`会根据你的配置选择合适的编译器（如GCC、Clang或MSVC）

## 什么是FFmpeg

[FFmpeg百度百科的解释]( https://baike.baidu.com/item/ffmpeg/2665727?fr=aladdinFFmpeg)是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。采用LGPL或GPL许可证。它提供了录制、转换以及流化音视频的完整解决方案。

它包含了非常先进的音频/视频编解码库libavcodec，为了保证高可移植性和编解码质量，libavcodec里很多code都是从头开发的FFmpeg在Linux平台下开发，但它同样也可以在其它操作系统环境中编译运行，包括Windows、MacOS X等。这个项目最早由Fabrice Bellard发起，2004年至2015年间由Michael Niedermayer主要负责维护。许多FFmpeg的开发人员都来自MPlayer项目，而且当前FFmpeg也是放在MPlayer项目组的服务器上。项目的名称来自MPEG视频编码标准，前面的"FF"代表"Fast Forward"。

FFmpeg是一套可以用来记录、转换[数字音频](https://baike.baidu.com/item/数字音频/5942163?fromModule=lemma_inlink)、视频，并能将其转化为流的开源计算机[程序](https://baike.baidu.com/item/程序/13831935?fromModule=lemma_inlink)。它包括了领先的音/视频编码库libavcodec等。

1. **libavformat**：用于各种音视频[封装格式](https://baike.baidu.com/item/封装格式/7015654?fromModule=lemma_inlink)的生成和解析，包括获取解码所需信息以生成解码[上下文](https://baike.baidu.com/item/上下文/2884376?fromModule=lemma_inlink)结构和读取音视频帧等功能；
2. **libavcodec**：用于各种类型声音/图像编[解码](https://baike.baidu.com/item/解码/10944752?fromModule=lemma_inlink)；
3. **libavutil**：包含一些公共的工具函数；
4. **libswscale**：用于视频场景比例缩放、色彩映射转换；
5. **libpostproc**：用于后期效果处理；
6. **ffmpeg**：该项目提供的一个工具，可用于[格式转换](https://baike.baidu.com/item/格式转换/55388491?fromModule=lemma_inlink)、解码或[电视卡](https://baike.baidu.com/item/电视卡/817994?fromModule=lemma_inlink)即时编码等；
7. **ffsever**：一个 HTTP 多媒体即时广播串流服务器；
8. **ffplay**：是一个简单的播放器，使用ffmpeg 库解析和解码，通过[SDL](https://baike.baidu.com/item/SDL/224181?fromModule=lemma_inlink)显示；

总之，学习音视频开发，FFmpeg是绕不过去的坎。

### 交叉编译FFmpeg库

[版本下载链接](https://www.ffmpeg.org/releases/)

交叉编译FFmpeg库是一件比较困难的事情，不同的FFmpeg版本和NDK版本，都会使编译指令存在细微差异。

我这里使用的`ffmpeg-4.0.2.tar.bz2`，NDK版本使用的是`android-ndk-r17c-linux-x86_64.zip`。

<img src="https://gitee.com/silent-learner/imgs/raw/master/2025图片/20250508214545576.png"/>

在根目录下执行shell脚本命令，只需要修改里面的路径：

```sh
#!/bin/bash

# 首先定义一个NDK目录的变量 NDK_ROOT
NDK_ROOT=/home/lux/Android/ndk/android-ndk-r17c


# 此变量执行ndk中的交叉编译gcc所在目录  32位 
#TOOLCHAIN=$NDK_ROOT/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64

#此变量执行ndk中的交叉编译gcc所在目录  64位
TOOLCHAIN=$NDK_ROOT/toolchains/aarch64-linux-android-4.9/prebuilt/linux-x86_64

#从as的 externalNativeBuild/xxx/build.ninja，  反正下面的配置，可以压制警告的意思   32位 
#FLAGS="-isystem $NDK_ROOT/sysroot/usr/include/arm-linux-androideabi -D__ANDROID_API__=21 -g -DANDROID -ffunction-sections -funwind-tables -fstack-protector-strong -no-canonical-prefixes -march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -mthumb -Wa,--noexecstack -Wformat -Werror=format-security  -O0 -fPIC"
#INCLUDES=" -isystem $NDK_ROOT/sources/android/support/include"

# 设置编译标志，适配aarch64架构
FLAGS="-isystem $NDK_ROOT/sysroot/usr/include/aarch64-linux-android -D__ANDROID_API__=21 -g -DANDROID -ffunction-sections -funwind-tables -fstack-protector-strong -no-canonical-prefixes -O0 -fPIC"
INCLUDES=" -isystem $NDK_ROOT/sources/android/support/include"

# 1.定义编译后，所存放的目录
PREFIX=./android/arm

# 2.--enable-small 优化大小 非常重要，必须优化才行的哦
# 3.--disable-programs 不编译ffmpeg程序（命令行工具），我们是需要获取静态、动态库
# 4.--disable-avdevice 关闭avdevice模块，此模块在android中无用
# 5.--disable-encoders 关闭所有编码器（播放不需要编码）
# 6.--disable-muxers 关闭所有复用器（封装器），不需要生成mp4这样的文件，所有关闭
# 7.--disable-filters 关闭所有滤镜
# 8.--enable-cross-compile 开启交叉编译（ffmpeg是跨平台的，注意：并不是所有库都有这么happy的选项）
# 9.--cross-prefix 看右边的值就知道是干嘛的，gcc的前缀..
# 10.disable-shared / enable-static 这个不写也可以，默认就是这样的，（代表关闭动态库，开启静态库）
# 11.--sysroot
# 12.--extra-cflags 会传给gcc的参数
# 13.--arch  --target-os

#./configure \
#--prefix=$PREFIX \
#--enable-small \
#--disable-programs \
#--disable-avdevice \
#--disable-encoders \
#--disable-muxers \
#--disable-filters \
#--enable-cross-compile \
#--cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
#--disable-shared \
#--enable-static \
#--sysroot=$NDK_ROOT/platforms/android-21/arch-arm \
#--extra-cflags="$FLAGS $INCLUDES" \
#--extra-cflags="-isysroot $NDK_ROOT/sysroot/" \
#--arch=arm \
#--target-os=android


# 配置并编译FFmpeg
./configure \
--prefix=$PREFIX \
--enable-small \
--disable-programs \
--disable-avdevice \
--disable-encoders \
--disable-muxers \
--disable-filters \
--enable-cross-compile \
--cross-prefix=$TOOLCHAIN/bin/aarch64-linux-android- \
--disable-shared \
--enable-static \
--sysroot=$NDK_ROOT/platforms/android-21/arch-arm64 \
--extra-cflags="$FLAGS $INCLUDES" \
--extra-cflags="-isysroot $NDK_ROOT/sysroot/" \
--arch=aarch64 \
--target-os=android

make clean
make install
```

对于FFmpeg的功能开启的一些的帮助选项，可以通过`./configure --help`查看：

```text
~/code/ffmpeg-4.0.2 git:[master]
./configure --help
Usage: configure [options]
Options: [defaults in brackets after descriptions]

Help options:
  --help                   print this message
  --quiet                  Suppress showing informative output   抑制显示信息性输出，使得输出更加简洁。
  --list-decoders          --list-decoders: 列出所有可用的解码器。
  --list-encoders:         列出所有可用的编码器。
  --list-hwaccels:         列出所有可用的硬件加速器。
  --list-demuxers:         列出所有可用的解复用器（用于解析多媒体文件格式）。
  --list-muxers:           列出所有可用的复用器（用于封装多媒体文件格式）。
  --list-parsers:          列出所有可用的解析器（用于处理多媒体流中的数据包）。
  --list-protocols:        列出所有可用的协议（支持的网络协议或文件访问方法）。
  --list-bsfs:             列出所有可用的比特流过滤器（用于修改编码比特流）。
  --list-indevs:           列出所有可用的输入设备（如摄像头、音频输入等）。
  --list-outdevs:          列出所有可用的输出设备（如屏幕、音频输出等）。
  --list-filters:          列出所有可用的滤镜（用于视频和音频的处理和效果添加）。
  --list-encoders          show all available encoders
  --list-hwaccels          show all available hardware accelerators
  --list-demuxers          show all available demuxers
  --list-muxers            show all available muxers
  --list-parsers           show all available parsers
  --list-protocols         show all available protocols
  --list-bsfs              show all available bitstream filters
  --list-indevs            show all available input devices
  --list-outdevs           show all available output devices
  --list-filters           show all available filters

Standard options:
  --logfile=FILE           log tests and output to FILE [ffbuild/config.log]
  --disable-logging        do not log configure debug information
  --fatal-warnings         fail if any configure warning is generated
  --prefix=PREFIX          install in PREFIX [/usr/local]
  --bindir=DIR             install binaries in DIR [PREFIX/bin]
  --datadir=DIR            install data files in DIR [PREFIX/share/ffmpeg]
  --docdir=DIR             install documentation in DIR [PREFIX/share/doc/ffmpeg]
  ......
  ......
  ......
```

编译成功后的静态文件

<img src="https://gitee.com/silent-learner/imgs/raw/master/2025图片/20250508214545546.png"/>

### 集成到Android

在现代的Android Studio中会默认使用cmake去管理C/C++源码的交叉编译。

**CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.22.1)


project("demo2")


# 引入FFmpeg的头文件
include_directories(${CMAKE_SOURCE_DIR}/include)


# 设置库路径时避免重复路径
set(LIB_PATH ${CMAKE_SOURCE_DIR}/${ANDROID_ABI})


# 链接目录设置为FFmpeg库所在的目录
link_directories(${LIB_PATH})


add_library(${CMAKE_PROJECT_NAME} SHARED
        # List C/C++ source files with relative paths to this CMakeLists.txt.
        native-lib.cpp)

target_link_libraries(${CMAKE_PROJECT_NAME}
        ${LIB_PATH}/libavutil.a
        ${LIB_PATH}/libavfilter.a
        ${LIB_PATH}/libavformat.a
        ${LIB_PATH}/libswresample.a
        ${LIB_PATH}/libswscale.a
        ${LIB_PATH}/libavcodec.a
        android
        log)
```

在**native-lib.cpp**中可以简单打印FFmpeg的版本：

```C++
#include <jni.h>
#include <string>

extern "C" {
#include "include/libavutil/avutil.h"
}


extern "C"
JNIEXPORT jstring JNICALL
Java_com_example_demo2_MainActivity_stringFromJNI(JNIEnv *env, jobject thiz) {
    std::string hello = "当前的FFmpeg的版本是：";
    hello.append(av_version_info());
    return env->NewStringUTF(hello.c_str());
}
```
