---
title: Linux中文件的查找
date: 2024-11-18 00:58:33
index_img: /img/siteicon/文件查找.webp
category:
  - Linux
  - 常见命令
tags:
  - Linux
abbrlink: 's3ax'
---

在使用linux时，经常需要进行文件查找。其中查找的命令主要有`find`和`grep`。他们都具有查找功能，但是查找的方向不一致。

## find 查找

- 主要用于查找文件和目录。
- 可以根据多种条件（如文件名、大小、修改时间等）来查找文件。
- 适用于文件系统的搜索

### 命令格式

```sh
find [路径] [表达式]
```

- **路径**：指定要搜索的目录或文件路径。
- **表达式**：指定查找条件，如 `-name`、`-type`、`-mtime` 等。

按文件名查找

**`-name`**：按文件名查找，支持通配符 `*` 和 `?`。

```sh
find /path/to/search -name "filename.txt"
## 查找当前目录及其子目录中所有扩展名为 .txt 的文件
find . -name "*.txt"
```

#### 按文件类型查找

- `f`：普通文件
- `d`：目录
- `l`：符号链接
- `c`：字符设备
- `b`：块设备

```sh
## 查找当前目录及其子目录中的所有目录
find . -type d
```

#### 按修改时间查找

**`-mtime`**：按文件的最后修改时间查找。

- `+n`：超过 n 天
- `-n`：少于 n 天
- `n`：正好 n 天

```sh
## 查找当前目录及其子目录中最后 7 天内修改过的文件
find . -mtime -7
## 查找当前目录及其子目录中超过 30 天未修改的文件
find . -mtime +30
```

#### 按访问时间查找

**`-atime`**：按文件的最后访问时间查找。

- `+n`：超过 n 天
- `-n`：少于 n 天
- `n`：正好 n 天

```sh
## 查找当前目录及其子目录中最后 7 天内访问过的文件
find . -atime -7
```

####  按文件大小查找

**`-size`**：按文件大小查找。

- `+n`：大于 n
- `-n`：小于 n
- `n`：正好 n

单位可以是 `c`（字节）、`k`（千字节）、`M`（兆字节）、`G`（吉字节）。

```sh
## 查找当前目录及其子目录中大于 100MB 的文件
find . -size +100M
```

#### 按权限查找

**`-perm`**：按文件权限查找。

- `-mode`：权限完全匹配
- `+mode`：权限部分匹配

```sh
## 查找当前目录及其子目录中权限为 755 的文件
find . -perm 755
## 查找当前目录及其子目录中具有可执行权限的文件
find . -perm +111
```

#### 按用户和组查找

- **`-user`**：按文件的所有者查找。
- **`-group`**：按文件的所属组查找。

```sh
##查找当前目录及其子目录中属于用户 `john` 的文件：
find . -user john
```

#### 组合条件

- **`-and`**：逻辑与（默认）
- **`-or`**：逻辑或
- **`!`**：逻辑非

```sh
##查找当前目录及其子目录中扩展名为 .txt 且大小大于 1MB 的文件
find . -name "*.txt" -size +1M
##查找当前目录及其子目录中扩展名为 .txt 或 .log 的文件
find . $ -name "*.txt" -or -name "*.log" $
##查找当前目录及其子目录中不属于用户 john 的文件
find . ! -user john
```

#### 执行命令

**`-exec`**：对找到的每个文件执行指定的命令。

- `{}`：占位符，表示当前找到的文件。
- `\;`：表示 `-exec` 命令结束。

```sh
##查找当前目录及其子目录中所有 .txt 文件并打印其内容
find . -name "*.txt" -exec cat {} \;

##查找当前目录及其子目录中所有 .txt 文件并删除它们：
find . -name "*.txt" -exec rm {} \;
## -ok：与 -exec 类似，但在执行命令前会提示用户确认
find . -name "*.txt" -ok rm {} \;
```

## grep 查找

- 主要用于在文件内容中查找特定的文本模式。
- 可以在文件中搜索匹配的行。
- 适用于文本内容的搜索。

### 命令格式

```sh
grep [选项] 模式 [文件...]
```

- **模式**：要搜索的文本模式。
- **文件**：指定要搜索的文件列表。

#### 基本搜索

**`-i`**：忽略大小写。

```sh
grep -i "pattern" file.txt
```

**`-w`**：匹配整个单词

```sh
grep -w "word" file.txt
```

**`-x`**：匹配整行

```sh
grep -x "line" file.txt
```

#### 显示上下文

- **`-A n`**：显示匹配行及其后的 n 行。

  ```sh
  grep -A 2 "pattern" file.txt
  ```

- **`-B n`**：显示匹配行及其前的 n 行。

  ```sh
  grep -B 2 "pattern" file.txt
  ```

- **`-C n`**：显示匹配行及其前后的 n 行。

  ```sh
  grep -C 2 "pattern" file.txt
  ```

#### 文件和目录

- **`-r` 或 `-R`**：递归搜索目录中的文件。

  ```sh
  grep -r "pattern" /path/to/directory
  ```

- **`-l`**：只输出包含匹配行的文件名。

  bash深色版本

  ```sh
  grep -l "pattern" *.txt
  ```

- **`-L`**：只输出不包含匹配行的文件名。

  bash深色版本

  ```sh
  grep -L "pattern" *.txt
  ```

#### 行号和计数

- **`-n`**：显示匹配行的行号。

  bash深色版本

  ```sh
  grep -n "pattern" file.txt
  ```

- **`-c`**：显示匹配行的数量。

  bash深色版本

  ```sh
  grep -c "pattern" file.txt
  ```

#### 反向匹配

- `-v`

  ：反向匹配，显示不包含匹配项的行。

  bash深色版本

  ```sh
  grep -v "pattern" file.txt
  ```

#### 正则表达式

- **`-E`**：使用扩展正则表达式。

  bash深色版本

  ```sh
  grep -E "pattern1|pattern2" file.txt
  ```

- **`-P`**：使用 Perl 兼容正则表达式（PCRE）。

  bash深色版本

  ```sh
  grep -P "\d+" file.txt
  ```

#### 其他选项

- **`--color`**：高亮显示匹配的文本。

  bash深色版本

  ```sh
  grep --color "pattern" file.txt
  ```

- **`-q`**：安静模式，不输出任何内容，只返回状态码。

  bash深色版本

  ```sh
  grep -q "pattern" file.txt && echo "Pattern found"
  ```

### 实际应用示例

1. **查找包含特定字符串的行**：

   bash深色版本

   ```sh
   grep "hello" file.txt
   ```

2. **忽略大小写查找**：

   bash深色版本

   ```sh
   grep -i "hello" file.txt
   ```

3. **递归搜索目录中的文件**：

   bash深色版本

   ```sh
   grep -r "hello" /path/to/directory
   ```

4. **显示匹配行的行号**：

   bash深色版本

   ```sh
   grep -n "hello" file.txt
   ```

5. **查找包含多个模式的行**：

   bash深色版本

   ```sh
   grep -E "pattern1|pattern2" file.txt
   ```

6. **查找不包含特定字符串的行**：

   bash深色版本

   ```sh
   grep -v "hello" file.txt
   ```

7. **查找包含特定模式的文件名**：

   bash深色版本

   ```sh
   grep -l "hello" *.txt
   ```

8. **查找包含数字的行**：

   bash深色版本

   ```sh
   grep -P "\d+" file.txt
   ```

9. **查找并替换文件中的内容**：

   bash深色版本

   ```sh
   grep -l "oldtext" *.txt | xargs sed -i 's/oldtext/newtext/g'
   ```
