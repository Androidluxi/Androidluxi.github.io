---
title: WiFi adb 连接
index_img: https://img.onlinedown.net/download/202306/174942-649d53b6c0d02.jpg
category:
  - 工具知识
  - adb
tags:
  - adb
abbrlink: b0b9
date: 2024-01-24 20:19:05
---

<meta name="referrer" content="no-referrer"/>

# WiFi adb 连接过程

{% note success %}  

设备和电脑需要连接相同的热点，保证处于同一局域网内。

{% endnote %}



1. 请求临时root权限。
2. adb remount命令用于重新挂载设备的/system分区为可读写模式。可以借用ADB工具向/system分区推送文件或进行必要的系统级别修改

```apl
adb root
adb remount
```

３.命令查询IP地址，`wlan开头的inet addr:后面的就是 IP 地址`

```apl
adb shell ifconfig
```

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/img_v3_027d_95f08eb9-53dc-4c91-a8c7-f6fb455041fg.jpg"/>

４.开启远程连接 接口5555，或者选择其他一个空闲的端口

```apl
adb tcpip 5555
```

5.指IP地址，然后查看是否连接成功。例如：`adb connect 192.168.108.89：5555`

```apl
adb connect **.***.**.**：5555 
```

6.如果连接成功，会显示两个设备

```apl
PS C:\Users\Sc\Desktop> adb devices
List of devices attached
0000000000000000        device
192.168.108.89:5555     device
```


7.退出远程连接

```apl
adb disconnect
```

