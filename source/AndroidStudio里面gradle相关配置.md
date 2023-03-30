对我自己掌握的关于gradle相关的知识进行一个整理。

下面是Android studio里面比较重要的gradle文件。会逐一的进行解析。

![image-20230320160144569](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320160144569.png)

1. build.gradle(模块里面)

   里面的具体内容参考我之前写的一篇博客：[详解build.gradle文件](https://blog.csdn.net/qq_43867812/article/details/126850708)。这个文件里面是对当前的module进行配置。

2. build.gradle（项目里面）

   这个文件添加所有子项目/模块通用的配置选项。可以看到他自动生成的里面添加了gradle的依赖，我的版本是7.2.1。当我们需要清除gradle生成的配置文件，也就是build文件夹，就会执行![image-20230320162313273](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320162313273.png)

   ```groovy
   // Top-level build file where you can add configuration options common to all sub-projects/modules.
   plugins {
       id 'com.android.application' version '7.2.1' apply false
       id 'com.android.library' version '7.2.1' apply false
   }
   // 运行gradle clean时，执行此处定义的task任务。
   // 该任务继承自Delete，删除根目录中的build目录。
   // 相当于执行Delete.delete(rootProject.buildDir)。
   // gradle使用groovy语言，调用method时可以不用加（）。
   task clean(type: Delete) {
       delete rootProject.buildDir
   }
   ```

3. gradle.properties

   主要是增加和修改一些可以在构建过程中直接使用的参数。具体怎么使用，暂时没有研究，也许未来会进行补充。

4. setting.gradle

   这个文件是我今天之前了解最少的，今天查了不少其他人的博客，终于大致弄懂了一些。

   首先第一个注意事项：在gradle7.1以后的版本中，发生了功能模块迁移。

   原来在工程build.gradle的buildscript和allprojects移动至setting.gradle并改名为pluginManagement 和dependencyResolutionManagement。里面的东西依旧可以按照原来的copy过来。

   [Android Gradle 7.1+新版本依赖变化]: https://blog.csdn.net/sinat_38167329/article/details/123175556

   下面我讲对里面的相关配置进行解析：

   1. pluginManagement ：

      `pluginManagement{}`语法块是专门用于管理整个项目插件的，只能出现在`settings.gradle`文件或”初始化脚本“中，并且在`settings.gradle`文件中`pluginManagement{}`必须是文件中的第一个块。

      

      - repositories{}语法块，用于指定仓库，有以下常用选项：
        - mavenLocal()：本地Maven仓库（ ${user.home}/.m2/repository ）
        - mavenCentral()：中央Maven仓库（ http://repo1.maven.org/maven2 ）
        - maven { url 'https://...' }：可用于Maven私服、镜像服务器等
        - ivy {url "../local-repo"}：本地的ivy仓库
        - ivy {url "http://repo.mycompany.com/repo"}：远程的ivy仓库
        - google()：google仓库（https://maven.google.com）
      - dependencies{}语法块，用于指定要使用的插件，由classpath关键字指定，格式为：classpath 'group:name:version'

      [Gradle入门教程]: https://blog.csdn.net/LiMubai_CN/article/details/102790699

      那么buildscript中的repositories和allprojects的repositories的作用和区别是什么呢？
       答：

      1. `buildscript`里是gradle脚本执行所需依赖，分别是对应的maven库和插件
      2. `allprojects`里是项目本身需要的依赖，比如我现在要依赖我自己maven库的`toastutils`库，那么我应该将`maven {url 'https://d l.bin tray.com/calvinning/maven'}`写在这里，而不是`buildscript`中，不然找不到。

      [buildscript和allprojects的作用和区别是什么？]: https://www.jianshu.com/p/ee57e4de78a3


   ![image-20230320163037528](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320163037528.png)

上图应该和自动生成的有些许区别。多了下面的代码。

```
jcenter()
maven { url 'https://jitpack.io' }
```

这里就是导入了[jitpack.io](https://link.zhihu.com/?target=https%3A//jitpack.io/)。

科普记录：在之前的Android gradle里面生成的应该是jcenter()，但是现在MavenCentral，原因是Jcenter服务即将关闭，谷歌没有收购他，所以改用mavenCentral，所以之前很多第三方库都不能使用了，不过很多个人开发者将自己开发的库也移植到了新的服务器。例如[jitpack.io](https://link.zhihu.com/?target=https%3A//jitpack.io/)。所以我们可以通过上面的代码导入地址。

[Jcenter服务即将关闭，改用mavenCentral](https://zhuanlan.zhihu.com/p/363156372)

下面的两行代码理解应该是比较简单的。include是groovy里面的代码。

![image-20230320171741487](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320171741487.png)

在 [Groovy](https://so.csdn.net/so/search?q=Groovy&spm=1001.2101.3001.7020) 语法中 , 就是调用了 include 方法 , 传入了 ‘:app’ 字符串作为参数 ;

当我们在项目中new 一个module时，下面会增加一个新的include。

对我自己掌握的关于gradle相关的知识进行一个整理。

下面是Android studio里面比较重要的gradle文件。会逐一的进行解析。

![image-20230320160144569](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320160144569.png)

1. build.gradle(模块里面)

   里面的具体内容参考我之前写的一篇博客：[详解build.gradle文件](https://blog.csdn.net/qq_43867812/article/details/126850708)。这个文件里面是对当前的module进行配置。

2. build.gradle（项目里面）

   这个文件添加所有子项目/模块通用的配置选项。可以看到他自动生成的里面添加了gradle的依赖，我的版本是7.2.1。当我们需要清除gradle生成的配置文件，也就是build文件夹，就会执行![image-20230320162313273](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320162313273.png)

   ```groovy
   // Top-level build file where you can add configuration options common to all sub-projects/modules.
   plugins {
       id 'com.android.application' version '7.2.1' apply false
       id 'com.android.library' version '7.2.1' apply false
   }
   // 运行gradle clean时，执行此处定义的task任务。
   // 该任务继承自Delete，删除根目录中的build目录。
   // 相当于执行Delete.delete(rootProject.buildDir)。
   // gradle使用groovy语言，调用method时可以不用加（）。
   task clean(type: Delete) {
       delete rootProject.buildDir
   }
   ```

3. gradle.properties

   主要是增加和修改一些可以在构建过程中直接使用的参数。具体怎么使用，暂时没有研究，也许未来会进行补充。

4. setting.gradle

   这个文件是我今天之前了解最少的，今天查了不少其他人的博客，终于大致弄懂了一些。

   首先第一个注意事项：在gradle7.1以后的版本中，发生了功能模块迁移。

   原来在工程build.gradle的buildscript和allprojects移动至setting.gradle并改名为pluginManagement 和dependencyResolutionManagement。里面的东西依旧可以按照原来的copy过来。

   [Android Gradle 7.1+新版本依赖变化]: https://blog.csdn.net/sinat_38167329/article/details/123175556

   下面我讲对里面的相关配置进行解析：

   1. pluginManagement ：

      `pluginManagement{}`语法块是专门用于管理整个项目插件的，只能出现在`settings.gradle`文件或”初始化脚本“中，并且在`settings.gradle`文件中`pluginManagement{}`必须是文件中的第一个块。

      

      - repositories{}语法块，用于指定仓库，有以下常用选项：
        - mavenLocal()：本地Maven仓库（ ${user.home}/.m2/repository ）
        - mavenCentral()：中央Maven仓库（ http://repo1.maven.org/maven2 ）
        - maven { url 'https://...' }：可用于Maven私服、镜像服务器等
        - ivy {url "../local-repo"}：本地的ivy仓库
        - ivy {url "http://repo.mycompany.com/repo"}：远程的ivy仓库
        - google()：google仓库（https://maven.google.com）
      - dependencies{}语法块，用于指定要使用的插件，由classpath关键字指定，格式为：classpath 'group:name:version'

      [Gradle入门教程]: https://blog.csdn.net/LiMubai_CN/article/details/102790699

      那么buildscript中的repositories和allprojects的repositories的作用和区别是什么呢？
       答：

      1. `buildscript`里是gradle脚本执行所需依赖，分别是对应的maven库和插件
      2. `allprojects`里是项目本身需要的依赖，比如我现在要依赖我自己maven库的`toastutils`库，那么我应该将`maven {url 'https://d l.bin tray.com/calvinning/maven'}`写在这里，而不是`buildscript`中，不然找不到。

      [buildscript和allprojects的作用和区别是什么？]: https://www.jianshu.com/p/ee57e4de78a3


   ![image-20230320163037528](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320163037528.png)

上图应该和自动生成的有些许区别。多了下面的代码。

```
jcenter()
maven { url 'https://jitpack.io' }
```

这里就是导入了[jitpack.io](https://link.zhihu.com/?target=https%3A//jitpack.io/)。

科普记录：在之前的Android gradle里面生成的应该是jcenter()，但是现在MavenCentral，原因是Jcenter服务即将关闭，谷歌没有收购他，所以改用mavenCentral，所以之前很多第三方库都不能使用了，不过很多个人开发者将自己开发的库也移植到了新的服务器。例如[jitpack.io](https://link.zhihu.com/?target=https%3A//jitpack.io/)。所以我们可以通过上面的代码导入地址。

[Jcenter服务即将关闭，改用mavenCentral](https://zhuanlan.zhihu.com/p/363156372)

下面的两行代码理解应该是比较简单的。include是groovy里面的代码。

![image-20230320171741487](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230320171741487.png)

在 [Groovy](https://so.csdn.net/so/search?q=Groovy&spm=1001.2101.3001.7020) 语法中 , 就是调用了 include 方法 , 传入了 ‘:app’ 字符串作为参数 ;

当我们在项目中new 一个module时，下面会增加一个新的include。

