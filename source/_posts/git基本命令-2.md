---
title: git基本命令-2
index_img: https://s2.loli.net/2024/02/19/qb57SynQeu8hHc4.png
category:
  - 工具知识
  - Git
tags:
  - Git
date: 2024-02-01 21:51:12
---

<meta name="referrer" content="no-referrer"/>

## 一、Git 基本概念


在介绍如何进行git版本回退前，我们需要先了解下git中的4个区：

- 工作区（Working Area）：就相当于我们的工作空间的目录，我们代码本地存放的地方。


- 暂存区（Stage）：也称作Index，用来跟踪已暂存的文件，一般是存在.git下的index文件，所以有时也称暂存区为索引。


- 本地仓库（Local Repository）


- 远程仓库（Remote Repository）

我们还需要了解下git文件的5种状态

- 未修改（Origin）
- 已修改（Modified）
- 已暂存（Staged）
- 已提交（Committed）
- 已推送（Pushed）

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240201231605.png"/>

## 二、工作流程

<img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240201233759.png"/>

## 三、基本命令

### 创建仓库命令

下表列出了 git 创建仓库的命令：

| 命令        | 说明                                   |
| :---------- | :------------------------------------- |
| `git init`  | 初始化仓库                             |
| `git clone` | 拷贝一份远程仓库，也就是下载一个项目。 |

------

### 提交与修改

Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。

下表列出了有关创建与提交你的项目的快照的命令：

| 命令                                | 说明                                     |
| :---------------------------------- | :--------------------------------------- |
| `git add`                           | 添加文件到暂存区                         |
| `git status`                        | 查看仓库当前的状态，显示有变更的文件。   |
| `git diff`                          | 比较文件的不同，即暂存区和工作区的差异。 |
| `git commit`                        | 提交暂存区到本地仓库。                   |
| `git reset`                         | 回退版本。                               |
| `git rm`                            | 将文件从暂存区和工作区中删除。           |
| `git mv`                            | 移动或重命名工作区文件。                 |
| `git checkout`                      | 分支切换。                               |
| `git switch （Git 2.23 版本引入）`  | 更清晰地切换分支。                       |
| `git restore （Git 2.23 版本引入）` | 恢复或撤销文件的更改。                   |

### 提交日志

| 命令               | 说明                                 |
| :----------------- | :----------------------------------- |
| `git log`          | 查看历史提交记录                     |
| `git blame <file>` | 以列表形式查看指定文件的历史修改记录 |

### 远程操作

| 命令         | 说明               |
| :----------- | :----------------- |
| `git remote` | 远程仓库操作       |
| `git fetch`  | 从远程获取代码库   |
| `git pull`   | 下载远程代码并合并 |
| `git push`   | 上传远程代码并合并 |

## 四、版本回退

版本回退主要就是在`已提交`代码和`已推送`代码之间操作。

回退命令：

```gas
git reset [--soft | --mixed | --hard] [HEAD]
```

**--mixed** 为默认，可以不用带该参数

--soft 、--mixed以及--hard是三个恢复等级。

{% note success %} 

- 使用--soft就仅仅将头指针恢复，已经add的暂存区以及工作空间的所有东西都不变。
- 如果使用--mixed，就将头恢复掉，已经add的暂存区也会丢失掉，工作空间的代码什么的是不变的。
- 如果使用--hard，那么一切就全都恢复了，头变，aad的暂存区消失，代码什么的也恢复到以前状态。

{% endnote %}

具体实例：

1. 软回退：

```apl
git reset --mixed head #当前版本
git reset --mixed HEAD^ #回退到上一个版本
git reset --mixed HEAD^^ #回退到上上一个版本
git reset --mixed HEAD~3 #回退到往上3个版本
git reset --mixed HEAD~10 #回退到往上10个版本
git reset HEAD^            # 回退所有内容到上一个版本  
git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本  
git  reset  052e           # 回退到指定版本 052e
```

2. 硬回退

```apl
git reset --hard HEAD~3  # 回退上上上一个版本  
git reset –hard bae128  # 回退到某个版本回退点之前的所有信息。 bae128
git reset --hard origin/master    # 将本地的状态回退到和远程的一样 
```

**HEAD 说明：**

- HEAD 表示当前版本

- HEAD^ 上一个版本

- HEAD^^ 上上一个版本

- HEAD^^^ 上上上一个版本

  

- 以此类推...

  

可以使用 ～数字表示

- HEAD~0 表示当前版本
- HEAD~1 上一个版本
- HEAD^2 上上一个版本
- HEAD^3 上上上一个版本
- 以此类推...

## 五、分支管理

### 查看当前分支

```shell
git branch
```

### 创建分支

```shell
git branch <branch>  #创建分支
git checkout -b <branch>  #创建并且切换到新的分支
```

### 切换分支:

```shell
git checkout (branchname)
# 分支切换回主分支master
git checkout master
```

### 合并分支

当分支切换回主分支的时候，可以将dev的修改提交合并到master分支上，使用：

```shell
# 合并dev到master
git merge dev
```

### 删除分支

```shell
git branch -d (branchname)
```

## 六、远程库管理

要查看当前配置有哪些远程仓库，可以用命令：

```shell
git remote
```

从远程仓库下载新分支与数据：

```shell
git fetch
```

该命令执行完后需要执行 git merge 远程分支到你所在的分支。

从远端仓库提取数据并尝试合并到当前分支：

```shell
git merge
```

删除远程仓库你可以使用命令：

```shell
git remote rm [别名]
```
