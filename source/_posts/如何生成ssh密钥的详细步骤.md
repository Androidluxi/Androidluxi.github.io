---
title: git生成ssh密钥详细步骤
index_img: https://s2.loli.net/2024/02/19/iQkUEx2qIaMlA7c.png
category:
  - 工具知识
  - Git
tags:
  - Git
abbrlink: 52bd
date: 2023-03-20 00:37:33
---

<meta name="referrer" content="no-referrer"/>

# git生成ssh密钥详细步骤

Git是一个开源的分布式版本控制系统，可以高效敏捷的处理任何项目，用于帮助管理Linux内核开发。而生成一个ssh是十分必要的，可以使电脑和code服务器之间建立安全的加密连接。

**git生成ssh密钥详细步骤**

1. 首先右键点击电脑桌面，点击选择"Git Bash Here"，打开git命令窗口;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180050.png"/>

2. 在git命令窗口配置用户，输入命令：**git config --global user.name "blkj"**。其中“blkj”是你自己要填的用户名;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180111.png"/>

3. 接着进行邮箱配置，输入命令：**git config --global user.email "blkj@boranet.com.cn"**。"blkj@boranet.com.cn"就是填入你自己的邮箱地址;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180133.png"/>

4. 此时在C:\Users\Administrator目录下会生成.gitconfig配置文件，这个文件不能删除;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180142.png"/>

5 .接着查看.gitconfig配置文件里的内容;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180426.png"/>

6. 继续在git命令窗口中输入命令：**ssh-keygen -t rsa -C "blkj@boranet.com.cn"**，就可以生成SSH公钥和私钥了;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180152.png"/>

7. 进入C:\Users\Administrator\.ssh目录，查看生成的SSH密钥;

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180157.png"/>

8 .在git命令窗口中输入命令：**cat ~/.ssh/id_rsa.pub**，就能查看公钥和私钥了。

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240120180207.png"/>