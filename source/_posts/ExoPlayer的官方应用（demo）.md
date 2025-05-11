---
title: ExoPlayer的官方应用（demo）
abbrlink: 4c15
date: 2024-02-23 23:40:53
index_img: /img/siteicon/ExoPlayer简单使用.png
category:
  - 音视频
tags:
  - Android
---

<meta name="referrer" content="no-referrer"/>

{% note success %} 

在ExoPlayer的官方GitHub地址上的README文档上，已经更新为V2.19.0版本

{% endnote %}

[项目地址](https://github.com/google/ExoPlayer)

### `在此版本ExoPlayer更新迁移到了AndroidX的Media3框架内`

[迁移后的项目地址](https://github.com/androidx/media)

## ExoPlayer 介绍

ExoPlayer是Android的应用程序级媒体播放器。它提供了一个 替代 Android 的 MediaPlayer API，用于播放音频和视频 本地和互联网。ExoPlayer 支持当前未支持的功能 受 Android 的 MediaPlayer API 支持，包括 DASH 和 SmoothStreaming 自适应播放。与 MediaPlayer API 不同，ExoPlayer 易于自定义 并扩展，并可以通过 Play 商店应用程序更新进行更新。

## 优点和缺点

与 Android 内置的 MediaPlayer 相比，ExoPlayer 具有许多优势：

- 设备特定问题更少，不同设备之间的行为差异更少 Android 的设备和版本。
- 能够将播放器与应用程序一起更新。因为 ExoPlayer 是一个库，您包含在应用程序 apk 中，您有 控制您使用的版本，您可以轻松地更新到更新的版本 版本作为定期应用程序更新的一部分。
- [能够自定义和扩展播放器](https://exoplayer.dev/customization.html)以适合您的用例。 ExoPlayer 是专门为此而设计的，它允许许多 要替换为自定义实现的组件。
- 支持[播放列表](https://exoplayer.dev/playlists.html)。
- 支持 [DASH](https://exoplayer.dev/dash.html) 和 [SmoothStreaming](https://exoplayer.dev/smoothstreaming.html)，两者都不支持 由 MediaPlayer 提供。还支持许多其他格式。查看[支持的 格式页面](https://exoplayer.dev/supported-formats.html)了解详细信息。
- 支持高级 [HLS](https://exoplayer.dev/hls.html) 功能，例如正确处理标签。`#EXT-X-DISCONTINUITY`
- 支持 [Android](https://exoplayer.dev/drm.html) 4.4（API 级别 19）和 高等。
- 能够快速与许多其他库集成，使用 官方扩展。例如，[IMA 扩展](https://github.com/google/ExoPlayer/tree/release-v2/extensions/ima)可以很容易地 使用[互动式媒体广告 SDK](https://developers.google.com/interactive-media-ads) 通过您的内容获利。

需要注意的是，也有一些缺点：

- 对于某些设备上的仅音频播放，ExoPlayer 可能会消耗大量 比 MediaPlayer 更多的电量。请参阅[电池消耗页面](https://exoplayer.dev/battery-consumption.html)，了解详情。
- 在您的应用程序中包含 ExoPlayer 会使 APK 大小增加几百KB。 这可能只是对于极其轻量级的应用程序而言。指导收缩 ExoPlayer 可以在 [APK 收缩页面上](https://exoplayer.dev/shrinking.html)找到。

# 简单使用

对于简单的用例，入门包括实现 步骤如下：`ExoPlayer`

1. 将 ExoPlayer 作为依赖项添加到您的项目中。
2. 创建实例。`ExoPlayer`
3. 将播放器附加到视图（用于视频输出和用户输入）。
4. 让播放器准备好播放音源。`MediaItem`
5. 完成后释放播放器。

## 将 ExoPlayer 添加为依赖项

### 添加 ExoPlayer 模块

开始使用 ExoPlayer 的最简单方法是将其添加为 gradle 依赖项。下面将添加 对完整库的依赖：`build.gradle`

```
implementation 'com.google.android.exoplayer:exoplayer:2.X.X'
```

哪里是您的首选版本（最新版本可以通过以下方式找到 请参阅[发行说明](https://github.com/google/ExoPlayer/tree/release-v2/RELEASENOTES.md)）。`2.X.X`

作为完整库的替代方法，您可以仅依赖库 您实际需要的模块。例如，以下将添加依赖项 在 Core、DASH 和 UI 库模块上，这可能是应用所必需的 只播放 DASH 内容：

```
implementation 'com.google.android.exoplayer:exoplayer-core:2.X.X'
implementation 'com.google.android.exoplayer:exoplayer-dash:2.X.X'
implementation 'com.google.android.exoplayer:exoplayer-ui:2.X.X'
```

当依赖于各个模块时，它们必须都是相同的版本。您可以 浏览 [Google Maven ExoPlayer 页面上](https://maven.google.com/web/index.html#com.google.android.exoplayer)的可用模块列表。这 Full Library 包括所有以 前缀的库模块 ， 除了 .`exoplayer-``exoplayer-transformer`

除了库模块之外，ExoPlayer 还具有依赖 外部库以提供其他功能。一些扩展是 可从 Maven 存储库获得，而其他存储库必须手动构建。 浏览[扩展目录](https://github.com/google/ExoPlayer/tree/release-v2/extensions/)及其各自的自述文件以了解详细信息。

### 启用 Java 8 支持

如果尚未启用，则需要根据ExoPlayer在所有文件中打开Java 8支持，方法是将以下内容添加到该部分：`build.gradle``android`

```
compileOptions {
  targetCompatibility JavaVersion.VERSION_1_8
}
```

### 启用 multidex

如果您的 Gradle 为 20 或更低，则应按顺序[启用 multidex](https://developer.android.com/studio/build/multidex) 以防止生成错误。`minSdkVersion`

## 创建播放器

您可以使用 创建一个实例，它提供 一系列自定义选项。下面的代码是最简单的示例 创建实例。`ExoPlayer``ExoPlayer.Builder`

```
ExoPlayer player = new ExoPlayer.Builder(context).build();
```

## 将播放器附加到视图

ExoPlayer库为媒体播放提供了一系列预构建的UI组件。包括：

`StyledPlayerView StyledPlayerControlView SubtitleView Surface StyledPlayerView`

```
// Bind the player to the view.
playerView.setPlayer(player);
```

您也可以作为独立组件使用，即 对于纯音频用例很有用。`StyledPlayerControlView`

使用 ExoPlayer 的预构建 UI 组件是可选的。

## 填充播放列表并准备播放器

在 ExoPlayer 中，每个媒体都用 .要播放 你需要构建一个相应的媒体，将其添加到 播放器，准备好播放器，然后调用以开始播放：`MediaItem``MediaItem``play`

```java
// Build the media item.
MediaItem mediaItem = MediaItem.fromUri(videoUri);
// Set the media item to be played.
player.setMediaItem(mediaItem);
// Prepare the player.
player.prepare();
// Start the playback.
player.play();
```

ExoPlayer直接支持播放列表，因此可以准备播放器 多个媒体项目一个接一个地播放：

```java
// Build the media items.
MediaItem firstItem = MediaItem.fromUri(firstVideoUri);
MediaItem secondItem = MediaItem.fromUri(secondVideoUri);
// Add the media items to be played.
player.addMediaItem(firstItem);
player.addMediaItem(secondItem);
// Prepare the player.
player.prepare();
// Start the playback.
player.play();
```

播放列表可以在播放过程中更新，而无需准备 再次播放。在播放列表页面上阅读有关填充和操作[播放列表](https://exoplayer.dev/playlists.html)的详细信息。阅读更多关于以下情况下可用的不同选项的信息 在“媒体项[”页上构建媒体项](https://exoplayer.dev/media-items.html)，例如剪辑和附加字幕文件。

## 控制播放器

一旦准备好播放器，就可以通过调用播放器上的方法来控制播放。下面列出了一些最常用的方法。

- `play`以及开始和暂停播放。`pause`
- `seekTo`允许在媒体内寻求。
- `hasPrevious` ，并允许在 播放列表。`hasNext``previous``next`
- `setRepeatMode`控制媒体是否循环播放以及如何循环播放。
- `setShuffleModeEnabled`控制播放列表随机播放。
- `setPlaybackParameters`调整播放速度和音频音调。

如果玩家绑定到 或 ， 然后用户与这些组件的交互将导致相应的方法 要调用的播放器。`StyledPlayerView StyledPlayerControlView`

## 释放播放器

在不再需要播放器时释放播放器很重要，这样才能释放 启动有限的资源，例如供其他应用程序使用的视频解码器。这 可以通过调用 来完成。`ExoPlayer.release`
