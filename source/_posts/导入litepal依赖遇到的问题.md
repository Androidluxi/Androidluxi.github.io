---
title: 导入litepal依赖遇到的问题
date: 2023-03-31 15:18:49
index_img: /img/25.jpg
category:
- 坑
tags:
- Android
---

添加litepal时候遇到了这个问题。

![litepal问题_1](%E5%AF%BC%E5%85%A5litepal%E4%BE%9D%E8%B5%96%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/litepal%E9%97%AE%E9%A2%98_1.png)

参考资料：

- [发布库AAR至mavenCentral看这篇文章就可以了](https://zhuanlan.zhihu.com/p/22351830)

- [一篇文章看懂gradle](https://blog.csdn.net/oubin66/article/details/100171069?spm=1001.2014.3001.5502)

- [Gradle 官方文档](https://docs.gradle.org/current/dsl/index.html)

那么buildscript中的repositories和allprojects的repositories的作用和区别是什么呢？
 答：
 1、 `buildscript`里是gradle脚本执行所需依赖，分别是对应的maven库和插件
 2、 `allprojects`里是项目本身需要的依赖，比如我现在要依赖我自己maven库的`toastutils`库，那么我应该将`maven {url 'https://dl.bintray.com/calvinning/maven'}`写在这里，而不是`buildscript`中，不然找不到。

作者：CalvinNing
链接：https://www.jianshu.com/p/ee57e4de78a3
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



```
pluginManagement {
    repositories {
        gradlePluginPortal()
        google()
        mavenCentral()
        jcenter()
    }
}
//1.功能位置迁移，原来在工程 build.gradle 的 buildscript 和 allprojects 移动至setting.gradle并改名为pluginManagement 和dependencyResolutionManagement。
// 里面的东西依旧可以按照原来的copy过来。
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        jcenter()
        maven { url 'https://jitpack.io' }

    }
}
rootProject.name = "WeatherAPP"
// 上述字符串换中的冒号是用于分割目录的 , 如果再次创建一个 app2 目录 , 配置文件会自动变为
include ':app'
```

