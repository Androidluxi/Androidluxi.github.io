---
title: git生成ssh密钥详细步骤
date: 2023-03-20 00:37:33
index_img: /img/16.jpg
category:
- 工具知识
tags:
- Git

---

# git生成ssh密钥详细步骤

Git是一个开源的分布式版本控制系统，可以高效敏捷的处理任何项目，用于帮助管理Linux内核开发。而生成一个ssh是十分必要的，可以使电脑和code服务器之间建立安全的加密连接。

**git生成ssh密钥详细步骤**

1. 首先右键点击电脑桌面，点击选择"Git Bash Here"，打开git命令窗口;

![img](https://img.win7zhijia.cn/upload/20220421/772c6c426fde8d660671f89385273fdd.jpg)

2. 在git命令窗口配置用户，输入命令：**git config --global user.name "blkj"**。其中“blkj”是你自己要填的用户名;

![img](https://img.win7zhijia.cn/upload/20220421/a32fb223325189a64ee296baf6754a6f.jpg)

3. 接着进行邮箱配置，输入命令：**git config --global user.email "blkj@boranet.com.cn"**。"blkj@boranet.com.cn"就是填入你自己的邮箱地址;

![img](https://img.win7zhijia.cn/upload/20220421/56fbe9631fe2d39fc2f158efac3f5cdf.jpg)

4. 此时在C:\Users\Administrator目录下会生成.gitconfig配置文件，这个文件不能删除;

![img](https://img.win7zhijia.cn/upload/20220421/0f9649cc65fd967dad97fa910da18955.jpg)

5 .接着查看.gitconfig配置文件里的内容;

![img](https://img.win7zhijia.cn/upload/20220421/614bff258932c63b103320419fbc5ff6.png)

6. 继续在git命令窗口中输入命令：**ssh-keygen -t rsa -C "blkj@boranet.com.cn"**，就可以生成SSH公钥和私钥了;

![img](https://img.win7zhijia.cn/upload/20220421/7435b94073ca51bc370191e583edcc7d.jpg)

7. 进入C:\Users\Administrator\.ssh目录，查看生成的SSH密钥;

![img](https://img.win7zhijia.cn/upload/20220421/dfb8a34dae9a13e0ed3307671942cb74.jpg)

8 .在git命令窗口中输入命令：**cat ~/.ssh/id_rsa.pub**，就能查看公钥和私钥了。

![img](https://img.win7zhijia.cn/upload/20220421/3270f60eb0cc1827c8c12d69366f0848.jpg)