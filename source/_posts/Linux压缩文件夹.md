---
title: Linux压缩和解压缩命令
date: 2024-11-17 21:02:55
index_img: /img/siteicon/linux压缩.webp
category:
  - Linux
  - 常见命令
tags:
  - Linux
abbrlink: 's1ax'
---

## 1. 介绍

在Linux操作系统中，压缩文件夹是一种非常常见且有用的操作。通过压缩文件夹，可以将多个文件和子文件夹打包成一个压缩文件，从而方便传输、存储和备份。本文将详细介绍如何在Linux中使用不同的命令和工具来压缩文件文件夹。

## 2. 使用tar命令压缩文件/文件夹

### 2.1 使用tar命令压缩

在Linux中，tar命令是一个用于打包文件和目录的常用工具。它可以将多个文件和目录打包成一个压缩文件，并保留文件和目录的层次结构。

以下是使用tar命令打包文件夹的基本语法：

```tex
tar -cvf 压缩文件名.tar 目录名/
```

- `-c`：创建一个新的归档文件。
- `-v`：显示处理过程中的详细信息（verbose 模式）。
- `-f`：指定归档文件的名称。

#### 示例1

打包单个文件

```sh
tar -cvf myarchive.tar myfile.txt
```

打包多个文件和目录

```sh
tar -cvf myarchive.tar file1.txt file2.txt directory1/
```

打包整个目录

```sh
tar -cvf myarchive.tar /path/to/directory
```

上面打包后的压缩包都是`.tar`后缀，不同的后缀表示压缩时候采用了不同的压缩算法。除了`.tar`后缀，常见的还有`tar.gz`、`.gz`、`bz2`。

#### 其他常用选项参数

- `-z`：使用gzip压缩归档文件。
- `-j`：使用bzip2压缩归档文件。
- `-J`：使用xz压缩归档文件。

#### 示例2

创建gzip压缩的归档文件

```sh
tar -cvzf myarchive.tar.gz file1.txt file2.txt  /path/to/directory
```

创建bzip2压缩的归档文件

```sh
tar -cjvf myarchive.tar.bz2 file1.txt file2.txt directory1/
```

创建xz压缩的归档文件

```
tar -cJvf myarchive.tar.xz file1.txt file2.txt directory1/
```



### 2.2使用tar命令解压缩

#### 解压选项参数

- `-x`：解压归档文件。

1. **解压普通的tar文件**

   ```sh
   tar -xvf myarchive.tar
   ```

2. **解压gzip压缩的tar文件**

   ```sh
   tar -xzvf myarchive.tar.gz
   ```

3. **解压bzip2压缩的tar文件**

   ```sh
   tar -xjvf myarchive.tar.bz2
   ```

4. **解压xz压缩的tar文件**

   ```sh
   tar -xJvf myarchive.tar.xz
   ```

### 2.3列出归档文件内容

- `-t`：列出归档文件中的内容。

```
tar -tvf myarchive.tar
```

