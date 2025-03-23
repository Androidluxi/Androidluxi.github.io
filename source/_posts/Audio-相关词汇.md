---
title: Audio 相关词汇
abbrlink: 1f49
index_img: /img/siteicon/29.png
date: 2024-02-24 15:58:56
category:
  - Android
tags:
  - 音视频
---

**ABR**  

自适应比特率。ABR算法是一种在播放过程中从多个音轨中进行选择的算法，其中每个音轨以不同的比特率呈现相同的媒体。

**Adaptive streaming**   	自适应流媒体

在自适应流媒体中，可以有多个音轨以不同的比特率呈现相同的媒体。在播放过程中，使用ABR算法动态选择所选曲目。

**Access unit** 	访问单元

媒体容器中的数据项。通常指可以解码并呈现给用户的一小段压缩媒体比特流(视频图片或可播放的音频片段)。

**AV1**

AOMedia Video 1 [编解码器](https://exoplayer.dev/glossary.html#codec)。有关更多信息，请参阅[维基百科页面](https://en.wikipedia.org/wiki/AV1)。

**AVC**

高级视频编码，也称为 H.264 视频[编解码器](https://exoplayer.dev/glossary.html#codec)。有关更多信息，请参阅[维基百科页面](https://en.wikipedia.org/wiki/Advanced_Video_Coding)。

**Codec** 	编解码器

以下两个定义是最常用的：

- 用于编码或解码[接入单元](https://exoplayer.dev/glossary.html#access-unit)的硬件或软件组件。
- 音频或视频示例格式规范。

**Container**	容器

一种媒体容器格式，例如 MP4 和 Matroska。这种格式称为 容器格式，因为它们包含一个或多个[媒体轨道](https://exoplayer.dev/glossary.html#track)， 其中每个轨道使用特定的[编解码器](https://exoplayer.dev/glossary.html#codec)（例如 AAC 音频和 H.264 MP4 文件中的视频）。请注意，某些媒体格式都是容器格式 和编解码器 （e.g. MP3）。

**DASH**

基于 HTTP 的动态[自适应流式处理](https://exoplayer.dev/glossary.html#adaptive-streaming)。行业驱动 自适应流协议。它由 ISO/IEC 23009 定义，可以找到 在 [ISO 公开可用标准页面上](https://standards.iso.org/ittf/PubliclyAvailableStandards/)。

**DRM**

数字版权管理。有关更多信息，请参阅[维基百科页面](https://en.wikipedia.org/wiki/Digital_rights_management)。

**Gapless playback** 	无缝播放

曲目结束和/或下[一首曲目](https://exoplayer.dev/glossary.html#track)开始的过程 跳过轨道以避免轨道之间出现静默间隙。有关更多信息，请参阅[维基百科页面](https://en.wikipedia.org/wiki/Gapless_playback)。

**HEVC**

高效视频编码，也称为 H.265 视频[编解码器](https://exoplayer.dev/glossary.html#codec)。

**HLS**

HTTP 实时流。Apple 的[自适应流媒体](https://exoplayer.dev/glossary.html#adaptive-streaming)协议。有关更多信息，请参阅 [Apple 文档](https://developer.apple.com/streaming/)。

**Manifest**

一个文件，用于定义[自适应流协议](https://exoplayer.dev/glossary.html#adaptive-streaming)中媒体的结构和位置。示例包括 [DASH](https://exoplayer.dev/glossary.html#dash) [MPD](https://exoplayer.dev/glossary.html#mpd) 文件、[HLS](https://exoplayer.dev/glossary.html#hls) 多变量播放列表文件和平[滑流式处理](https://exoplayer.dev/glossary.html#smooth-streaming)清单文件。不要与 AndroidManifest XML 文件。

**MPD**

媒体演示说明中使用的[清单](https://exoplayer.dev/glossary.html#manifest)文件格式 [DASH](https://exoplayer.dev/glossary.html#dash) [自适应流协议](https://exoplayer.dev/glossary.html#adaptive-streaming)。

**PCM**

脉冲编码调制。有关更多信息，请参阅[维基百科页面](https://en.wikipedia.org/wiki/Pulse-code_modulation)。

**Smooth Streaming**		平滑流式处理

Microsoft 的[自适应流式处理](https://exoplayer.dev/glossary.html#adaptive-streaming)协议。有关详细信息，请参阅 [Microsoft 文档](https://www.iis.net/downloads/microsoft/smooth-streaming)。

**Track**

媒体中的单个音频、视频、文本或元数据流。媒体 文件通常包含多个曲目。例如，视频轨道和音频 在视频文件中跟踪，或以不同语言录制多个音轨。在[自适应流中](https://exoplayer.dev/glossary.html#adaptive-streaming)，还有多个轨道 包含不同比特率的相同内容。
