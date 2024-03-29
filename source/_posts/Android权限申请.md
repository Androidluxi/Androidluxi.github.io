---
title: Android动态申请权限
date: 2023-03-28 01:00:26
index_img: /img/15.jpg
category:
- Android
tags:
- 基础


---

# 1.可以在AndroidManifest里面注册的权限：

```
<uses-permission android:name="android.permission.ACCEPT_HANDOVER" />
<!-- 允许呼应用继续在另一个应用中启动的呼叫 -->
<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION"/>
<!-- 允许应用访问后台的位置 -->
<uses-permission android:name="android.permission.ACCESS_BLOBS_ACROSS_USERS" />
<!-- 允许应用程序访问跨用户的数据 -->
<uses-permission android:name="android.permission.ACCESS_CHECKIN_PROPERTIES" />
<!-- 允许阅读/写入检查数据库中的"属性"表，以更改上传的值 -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<!-- 允许应用程序访问大致位置 -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<!-- 允许应用访问精确位置 -->
<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
<!-- 允许程序访问额外的定位提供者指令 -->
<uses-permission android:name="android.permission.ACCESS_MEDIA_LOCATION" />
```



```
<!-- 允许应用程序访问用户共享集合中持续存在的任何地理位置 -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!-- 允许应用程序访问有关网络的信息 -->
<uses-permission android:name="android.permission.ACCESS_NOTIFICATION_POLICY" />
<!-- 希望访问通知策略的应用程序的标记权限 此权限不支持托管配置文件 -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<!-- 允许应用程序访问有关 Wi-Fi 网络的信息 -->
<uses-permission android:name="android.permission.ACCOUNT_MANAGER" />
<!-- 允许应用程序调用到帐户授权人 -->
<uses-permission android:name="android.permission.ACTIVITY_RECOGNITION" />
<!-- 允许应用程序识别身体活动 -->
<uses-permission android:name="android.permission.ADD_VOICEMAIL" />
<!-- 允许应用程序向系统添加语音信箱 -->
<uses-permission android:name="android.permission.ANSWER_PHONE_CALLS" />
<!-- 允许应用程序接听来电 -->
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

```
<!-- 允许应用程序收集电池统计数据 -->
<uses-permission android:name="android.permission.BIND_ACCESSIBILITY_SERVICE" />
<!-- 确保只有系统可以绑定它 -->
<uses-permission android:name="android.permission.BIND_APPWIDGET" />
<!-- 允许应用程序告诉AppWidget服务哪些应用程序可以访问AppWidget数据 -->
<uses-permission android:name="android.permission.BIND_CARRIER_SERVICES" />
<!-- 允许与运营商应用中的服务绑定的系统过程将获得此权限 -->
<uses-permission android:name="android.permission.BIND_COMPANION_DEVICE_SERVICE" />
<!-- 任何 s 都必须确保只有系统才能与系统结合 -->
<uses-permission android:name="android.permission.BIND_CONTROLS" />
<!-- 允许系统UI请求第三方控制 -->
<uses-permission android:name="android.permission.BIND_DEVICE_ADMIN" />
<!-- 设备管理接收器必须要求 -->
<uses-permission android:name="android.permission.BLUETOOTH" />
```

```
<!-- 允许应用程序连接到配对蓝牙设备 -->
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<!-- 允许应用程序发现和配对蓝牙设备 -->
<uses-permission android:name="android.permission.BLUETOOTH_ADVERTISE" />
<!-- 需要能够向附近的蓝牙设备做广告 -->
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<!-- 需要能够连接到配对蓝牙设备 -->
<uses-permission android:name="android.permission.BLUETOOTH_PRIVILEGED" />
<!-- 允许应用程序在不进行用户交互的情况下对蓝牙设备进行配对，并允许或不允许电话簿访问或消息访问 -->
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<!-- 需要能够发现和配对附近的蓝牙设备 -->
```



```
<uses-permission android:name="android.permission.BODY_SENSORS" />
<!-- 允许应用程序从用户用来测量体内发生的情况（如心率）的传感器访问数据 -->
<uses-permission android:name="android.permission.BROADCAST_PACKAGE_REMOVED" />
<!-- 允许应用程序广播已删除应用程序包的通知 -->
<uses-permission android:name="android.permission.BROADCAST_SMS" />
<!-- 允许应用程序广播短信收据通知 -->
<uses-permission android:name="android.permission.BROADCAST_STICKY" />
<!-- 允许应用程序广播粘性意图 -->
<uses-permission android:name="android.permission.BROADCAST_WAP_PUSH" />
<!-- 允许应用程序广播 WAP 推送接收通知 -->
<uses-permission android:name="android.permission.CALL_COMPANION_APP" />
<!-- 允许实现 API 的应用有资格作为呼叫伴侣应用启用 -->
<uses-permission android:name="android.permission.CALL_PHONE" />
<!-- 允许应用程序启动电话呼叫，而无需通过拨号器用户界面，以便用户确认呼叫 -->
<uses-permission android:name="android.permission.CALL_PRIVILEGED" />
```

```
<!-- 允许应用程序拨打任何电话号码（包括紧急号码），而无需通过 Dialer 用户界面为用户确认已放置的呼叫 -->
<uses-permission android:name="android.permission.CAMERA" />
<!-- 需要能够访问摄像机设备 -->
<uses-permission android:name="android.permission.CAPTURE_AUDIO_OUTPUT" />
<!-- 允许应用程序捕获音频输出 -->
<uses-permission android:name="android.permission.CHANGE_COMPONENT_ENABLED_STATE" />
<!-- 允许应用程序更改应用程序组件（其自身除外）是否启用 -->
<uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
<!-- 允许应用程序修改当前配置 -->
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<!-- 允许应用程序更改网络连接状态 -->
<uses-permission android:name="android.permission.CHANGE_WIFI_MULTICAST_STATE" />
<!-- 允许应用程序进入 Wi-Fi 多播模式 -->
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
```

```
<!-- 允许应用程序更改 Wi-Fi 连接状态 -->
<uses-permission android:name="android.permission.CLEAR_APP_CACHE" />
<!-- 允许应用程序清除设备上所有已安装应用程序的缓存 -->
<uses-permission android:name="android.permission.CONTROL_LOCATION_UPDATES" />
<!-- 允许启用/禁用来自收音机的位置更新通知 -->
<uses-permission android:name="android.permission.DELETE_CACHE_FILES" />

<!-- 删除应用缓存文件的旧权限不再使用 -->
<uses-permission android:name="android.permission.DELETE_PACKAGES" />
<!-- 允许应用程序删除包 -->
<uses-permission android:name="android.permission.DIAGNOSTIC" />
<!-- 允许将 RW 应用到诊断资源 -->
<uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
<!-- 如果密钥防护系统不安全，允许应用程序禁用 -->
<uses-permission android:name="android.permission.DUMP" />
```

```
<!-- 允许应用程序从系统服务中检索状态转储信息 -->
<uses-permission android:name="android.permission.允许应用程序扩展或折叠状态栏" />
<!-- EXPAND_STATUS_BAR -->
<uses-permission android:name="android.permission.FACTORY_TEST" />
<!-- 作为制造商测试应用程序运行 -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<!-- 允许使用常规应用程序 -->
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<!-- 允许访问帐户服务中的帐户列表 -->
<uses-permission android:name="android.permission.GET_PACKAGE_SIZE" />
<!-- 允许应用程序找出任何包所使用的空间 -->
<uses-permission android:name="android.permission.GLOBAL_SEARCH" />
```



```
<!-- 此权限可用于内容提供商，以便全球搜索系统访问其数据 -->
<uses-permission android:name="android.permission.HIDE_OVERLAY_WINDOWS" />
<!-- 允许应用在上面绘制非系统覆盖窗口 -->
<uses-permission android:name="android.permission.HIGH_SAMPLING_RATE_SENSORS" />
<!-- 允许应用访问采样率大于 200 Hz 的传感器数据 -->
<uses-permission android:name="android.permission.INSTALL_LOCATION_PROVIDER" />
<!-- 允许应用程序将位置提供商安装到位置管理器中 -->
<uses-permission android:name="android.permission.INSTALL_PACKAGES" />
<!-- 允许应用程序安装包 -->
<uses-permission android:name="android.permission.INSTALL_SHORTCUT" />
<!-- 允许应用程序在启动器中安装快捷方式 -->
<uses-permission android:name="android.permission.INSTANT_APP_FOREGROUND_SERVICE" />
<!-- 允许即时应用创建前景服务 -->
<uses-permission android:name="android.permission.INTERACT_ACROSS_PROFILES" />
<!-- 允许在同一配置文件组中跨配置文件进行交互 -->
<uses-permission android:name="android.permission.INTERNET" />
<!-- 允许应用程序打开网络 -->
<uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES" />
<!-- 允许应用程序调用 -->
<uses-permission android:name="android.permission.LOADER_USAGE_STATS" />
<!-- 允许数据装载机读取包的访问日志。访问日志包含随着时间推移引用的页面集。 -->
<uses-permission android:name="android.permission.LOCATION_HARDWARE" />
```

```
<!-- 允许应用程序在硬件中使用位置功能 -->
<uses-permission android:name="android.permission.MANAGE_DOCUMENTS" />
<!-- 允许应用程序管理对文档的访问，通常作为文档拾取器的一部分 -->
<uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE" />
<!-- 允许应用程序在范围存储中广泛访问外部存储 -->
<uses-permission android:name="android.permission.MANAGE_MEDIA" />
<!-- 允许应用程序在未经用户确认的情况下修改和删除此设备或任何连接存储设备上的媒体文件 -->
<uses-permission android:name="android.permission.MANAGE_ONGOING_CALLS" />
<!-- 允许查询持续呼叫详细信息并管理持续呼叫 -->
<uses-permission android:name="android.permission.MANAGE_OWN_CALLS" />
<!-- 允许通过自我管理的 API 管理自己的呼叫的呼叫应用程序 -->
<uses-permission android:name="android.permission.MEDIA_CONTENT_CONTROL" />
<!-- 允许应用程序知道正在播放的内容并控制其播放 -->
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
```



```
<!-- 允许应用程序修改全球音频设置 -->
<uses-permission android:name="android.permission.MODIFY_PHONE_STATE" />
<!-- 允许修改电话状态 - 打开电源 -->
<uses-permission android:name="android.permission.MOUNT_FORMAT_FILESYSTEMS" />
<!-- 允许为可移动存储格式化文件系统 -->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
<!-- 允许安装和卸载文件系统以进行可拆卸存储 -->
<uses-permission android:name="android.permission.NFC" />
<!-- 允许应用程序在 NFC 上执行 I/O 操作 -->
<uses-permission android:name="android.permission.NFC_PREFERRED_PAYMENT_INFO" />
<!-- 允许申请接收 NFC 首选支付服务信息 -->
<uses-permission android:name="android.permission.NFC_TRANSACTION_EVENT" />
<!-- 允许应用程序接收 NFC 交易事件 -->
<uses-permission android:name="android.permission.PACKAGE_USAGE_STATS" />
<!-- 允许应用程序收集组件使用情况统计 -->
<uses-permission android:name="android.permission.QUERY_ALL_PACKAGES" />
<!-- 允许查询设备上的任何正常应用 -->
<uses-permission android:name="android.permission.READ_CALENDAR" />
```



```
<!-- 允许应用程序读取用户的日历数据 -->
<uses-permission android:name="android.permission.READ_CALL_LOG" />
<!-- 允许应用程序读取用户的通话记录。 -->
<uses-permission android:name="android.permission.READ_CONTACTS" />
<!-- 允许应用程序读取用户的联系人数据 -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<!-- 允许应用程序从外部存储中读取 -->
<uses-permission android:name="android.permission.READ_LOGS" />
<!-- 允许应用程序读取低级系统日志文件 -->
<uses-permission android:name="android.permission.READ_PHONE_NUMBERS" />
<!-- 允许读取设备的电话号码 -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- 仅允许阅读访问电话状态，包括当前的蜂窝网络信息、任何持续呼叫的状态 -->
<uses-permission android:name="android.permission.READ_PRECISE_PHONE_STATE" />
<!-- 允许阅读访问精确的手机状态 -->
<uses-permission android:name="android.permission.READ_SMS" />
<!-- 允许应用程序读取短信 -->
<uses-permission android:name="android.permission.READ_SYNC_SETTINGS" />
<!-- 允许应用程序读取同步设置 -->
<uses-permission android:name="android.permission.READ_SYNC_STATS" />
```



```
<!-- 允许应用程序读取同步统计数据 -->
<uses-permission android:name="android.permission.READ_VOICEMAIL" />
<!-- 允许应用程序在系统中读取语音信箱 -->
<uses-permission android:name="android.permission.REBOOT" />
<!-- 需要能够重新启动设备 -->
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<!-- 允许应用程序在系统完成启动后接收广播 -->
<uses-permission android:name="android.permission.RECEIVE_MMS" />
<!-- 允许应用程序监控传入的彩信 -->
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<!-- 允许应用程序接收短信 -->
<uses-permission android:name="android.permission.RECEIVE_WAP_PUSH" />
```



```
<!-- 允许应用程序接收 WAP 推送消息 -->
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<!-- 允许应用程序录制音频 -->
<uses-permission android:name="android.permission.REQUEST_COMPANION_PROFILE_WATCH" />
<!-- 允许应用请求通过"手表"与设备关联 -->
<uses-permission android:name="android.permission.REQUEST_COMPANION_RUN_IN_BACKGROUND" />
<!-- 允许配套应用在后台运行 -->
<uses-permission android:name="android.permission.REQUEST_COMPANION_START_FOREGROUND_SERVICES_FROM_BACKGROUND" />
<!-- 允许配套应用从后台开始前景服务 -->
<uses-permission android:name="android.permission.REQUEST_COMPANION_USE_DATA_IN_BACKGROUND" />
```

```
<!-- 允许配套应用在后台使用数据 -->
<uses-permission android:name="android.permission.REQUEST_DELETE_PACKAGES" />
<!-- 允许应用程序请求删除包 -->
<uses-permission android:name="android.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS" />
<!-- 申请必须持有才能使用的权 -->
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
<!-- 允许应用程序请求安装包 -->
<uses-permission android:name="android.permission.REQUEST_OBSERVE_COMPANION_DEVICE_PRESENCE" />

<!-- 允许应用程序订阅有关其关联配套设备的存在状态更改的通知 -->
<uses-permission android:name="android.permission.REQUEST_PASSWORD_COMPLEXITY" />
```

```
<!-- 允许应用程序请求屏幕锁的复杂性，并提示用户将屏幕锁更新到一定的复杂性级别 -->
<uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM" />
<!-- 允许应用程序使用精确的报警 API -->
<uses-permission android:name="android.permission.SEND_RESPOND_VIA_MESSAGE" />
<!-- 允许应用程序 （电话） 向其他应用程序发送请求，以便在来电期间处理响应消息操作 -->
<uses-permission android:name="android.permission.SEND_SMS" />
<!-- 允许应用程序发送短信 -->
<uses-permission android:name="android.permission.SET_ALARM" />
<!-- 允许应用程序广播意图为用户设置闹钟 -->
<uses-permission android:name="android.permission.SET_ALWAYS_FINISH" />
<!-- 允许应用程序控制在后台后立即完成活动 -->
<uses-permission android:name="android.permission.SET_ANIMATION_SCALE" />
<!-- 修改全局动画缩放因子 -->
<uses-permission android:name="android.permission.SET_DEBUG_APP" />
<!-- 配置调试应用程序 -->
<uses-permission android:name="android.permission.SET_PROCESS_LIMIT" />
```



```
<!-- 允许应用程序设置可以运行的最大数量（不需要）应用程序过程 -->
<uses-permission android:name="android.permission.SET_TIME" />
<!-- 允许应用程序直接设置系统时间 -->
<uses-permission android:name="android.permission.SET_TIME_ZONE" />
<!-- 允许应用程序直接设置系统时区 -->
<uses-permission android:name="android.permission.SET_WALLPAPER" />
<!-- 允许应用程序设置壁纸 -->
<uses-permission android:name="android.permission.SET_WALLPAPER_HINTS" />
<!-- 允许应用程序设置壁纸提示 -->
<uses-permission android:name="android.permission.SIGNAL_PERSISTENT_PROCESSES" />
```



```
<!-- 允许应用程序请求向所有持久性过程发送信号 -->
<uses-permission android:name="android.permission.START_FOREGROUND_SERVICES_FROM_BACKGROUND" />
<!-- 允许应用程序随时从后台开始前景服务 -->
<uses-permission android:name="android.permission.START_VIEW_PERMISSION_USAGE" />
<!-- 允许持有人启动应用程序的权限使用屏幕 -->
<uses-permission android:name="android.permission.STATUS_BAR" />
<!-- 允许应用程序打开、关闭或禁用状态栏及其图标 -->
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

```
<!-- 允许应用使用该类型创建窗口，该类型显示在所有其他应用的顶部 -->
<uses-permission android:name="android.permission.TRANSMIT_IR" />
<!-- 如果可用，允许使用设备的红外发射器 -->
<uses-permission android:name="android.permission.UPDATE_DEVICE_STATS" />
<!-- 允许应用程序更新设备统计数据 -->
<uses-permission android:name="android.permission.UPDATE_PACKAGES_WITHOUT_USER_ACTION" />
<!-- 允许应用程序通过该应用程序指示应用更新不需要用户操作 -->
<uses-permission android:name="android.permission.USE_BIOMETRIC" />
```



```
<!-- 允许应用使用设备支持的生物识别模式 -->
<uses-permission android:name="android.permission.USE_ICC_AUTH_WITH_DEVICE_IDENTIFIER" />
<!-- 允许读取设备标识符并使用基于 ICC 的身份验证（如 EAP-AKA） -->
<uses-permission android:name="android.permission.USE_SIP" />
<!-- 允许应用程序使用 SIP 服务 -->
<uses-permission android:name="android.permission.UWB_RANGING" />
<!-- 需要能够使用超宽带的设备 -->
<uses-permission android:name="android.permission.VIBRATE" />
```

```
<!-- 允许访问振动器 -->
<uses-permission android:name="android.permission.WAKE_LOCK" />
<!-- 允许使用电源经理唤醒锁来防止处理器睡觉或屏幕变暗 -->
<uses-permission android:name="android.permission.WRITE_APN_SETTINGS" />
<!-- 允许应用程序编写 apn 设置并读取现有 apn 设置的敏感字段（如用户和密码） -->
<uses-permission android:name="android.permission.WRITE_CALENDAR" />
<!-- 允许应用程序编写用户的日历数据 -->
<uses-permission android:name="android.permission.WRITE_CALL_LOG" />
<!-- 允许应用程序编写（但未读取）用户的呼叫日志数据 -->
<uses-permission android:name="android.permission.WRITE_CONTACTS" />
<!-- 允许应用程序编写用户的联系人数据 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```



```
<!-- 允许应用程序写入外部存储。 -->
<uses-permission android:name="android.permission.WRITE_GSERVICES" />
<!-- 允许应用程序修改 Google 服务地图 -->
<uses-permission android:name="android.permission.WRITE_SECURE_SETTINGS" />
<!-- 允许应用程序读取或编写安全系统设置 -->
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
<!-- 允许应用程序读取或编写系统设置 -->
<uses-permission android:name="android.permission.WRITE_SYNC_SETTINGS" />
<!-- 允许应用程序编写同步设置 -->
<uses-permission android:name="android.permission.WRITE_VOICEMAIL" />
<!-- 允许应用程序修改和删除系统中现有的语音信箱 -->
<uses-permission android:name="android.permission." />
```



# 2.动态申请权限

1. ## 判断是否有权限

   ```java
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE)
           != PackageManager.PERMISSION_GRANTED) {
       ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, 1);
   } else {
      return true;
   }
   ```

2. ## 申请权限

   ```java
   @Override
   public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
       super.onRequestPermissionsResult(requestCode, permissions, grantResults);
       if (requestCode == 1) {
           if (grantResults.length != 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
               Toast.makeText(this, "权限开启成功", Toast.LENGTH_SHORT).show();
   
           } else {
               Toast.makeText(this, "权限开启失败", Toast.LENGTH_SHORT).show();
           }
       }
   }
   ```