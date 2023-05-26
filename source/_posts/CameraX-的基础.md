---
title: CameraX 的基础
date: 2023-05-26 16:46:42
index_img: /img/27.png
category:
- Android
tags:
- Android进阶知识
---

# CameraX实现相机功能

预计实现功能：

1. 实时预览（缩放、点击聚焦）
2. 拍照（晃动拍照）、录像
3. 实现左右滑动，切换拍照、录像
4. 给拍照的图片加水印

## CameraX是什么，能解决什么问题

- Jetpack的一个支持库，最低版本要求Android5.0
- 默认的相机功能还是Camera2的能力，当然API都变了，同时提供CameraX Extensions拓展库可以添加各种特效，例如人像、HDR、夜间和美颜模式(从上图也可以看出，这是依赖OEM的)
- 绑定生命周期，所以Camera本身无需在生命周期中调用什么onPause onResume之类的样板代码，且忘记后会造成各种问题
- 抹平设备兼容性问题，无需在代码库中添加设备专属代码

## 准备工作

#### 1. 注意事项

- 最低支持API是21
- Android Studio 至少是3.6版本
- Java8环境

#### 2.导入依赖

```groovy
dependencies {
  // CameraX core library using the camera2 implementation
  def camerax_version = "1.3.0-alpha06"
  // The following line is optional, as the core library is included indirectly by camera-camera2
  implementation "androidx.camera:camera-core:${camerax_version}"
  implementation "androidx.camera:camera-camera2:${camerax_version}"
  // If you want to additionally use the CameraX Lifecycle library
  implementation "androidx.camera:camera-lifecycle:${camerax_version}"
  // If you want to additionally use the CameraX VideoCapture library
  implementation "androidx.camera:camera-video:${camerax_version}"
  // If you want to additionally use the CameraX View class
  implementation "androidx.camera:camera-view:${camerax_version}"
  // If you want to additionally add CameraX ML Kit Vision Integration
  implementation "androidx.camera:camera-mlkit-vision:${camerax_version}"
  // If you want to additionally use the CameraX Extensions library
  implementation "androidx.camera:camera-extensions:${camerax_version}"
}
```

#### 3.申请一些必要的权限

> <!--摄像头权限-->
> <uses-permission android:name="android.permission.CAMERA" />
> <!--具备摄像头-->
> <uses-feature android:name="android.hardware.camera.any" />
> <!--存储图像或者视频权限-->
> <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
> <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
> <!--录制音频权限-->
> <uses-permission android:name="android.permission.RECORD_AUDIO" />

## CameraX 结构

您可以使用 CameraX，借助名为“用例”的抽象概念与设备的相机进行交互。提供的用例如下：

- **预览**：接受用于显示预览的 Surface，例如 `PreviewView`。
- **图片分析**：为分析（例如机器学习）提供 CPU 可访问的缓冲区。
- **图片拍摄**：拍摄并保存照片。
- **视频拍摄**：通过 [`VideoCapture`](https://developer.android.google.cn/reference/androidx/camera/video/VideoCapture?hl=zh-cn) 拍摄视频和音频

我理解：cameraX给我们封装了四个不同的功能：实时预览、拍照、录像和图片分析。这些不同的功能可以组合到一起使用。

例如，应用中可以加入预览用例，以便让用户查看进入相机视野的画面；加入图片分析用例，以确定照片里的人物是否在微笑；还可以加入图片拍摄用例，以在人物微笑时拍摄照片。

## 配置 CameraX 用例

可以自行配置下面的内容：[配置选项  | Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/training/camerax/configuration?hl=zh-cn)

![image-20230526164804855](CameraX-%E7%9A%84%E5%9F%BA%E7%A1%80/image-20230526164804855.png)
