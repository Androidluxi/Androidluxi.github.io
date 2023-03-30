删除所关联的远程仓库地址

```
git remote rm origin
```

关联新的远程仓库

```
git remote add origin https://github.com/youName/youName.github.io.git
```

添加文件到暂存区

```
git add .
```

 提交暂存区到本地仓库。

```
git commit -m "commit message"
```

推送到远程库source分支

```
 git push --set-upstream origin Source
```

查看仓库当前的状态，显示有变更的文件。

```
git status
```

获取所有 Git 远程分支

借助以下命令，我们将从其仓库中获取远程分支。origin 是我们定位的远程分支的名称。如果我们有一个 upstream 远程名称，我们可以将其称为 git fetch upstream。

```text
git fetch origin
```

## **git checkout <name-of-your-branch>**

这也是最常用的 Git 命令之一。要在分支中工作，首先需要切换到该分支。我们通常使用 **git checkou**t 从一个分支切换到另一个分支。我们还可以使用它来检出文件和提交（commits）。

```
git checkout remotes/origin/Source
git checkout remotes/origin/master
```

**同时创建并切换到分支**

```
git checkout -b <name-of-your-branch>
```

## 列出 Git 可用的分支

```text
git branch -a
```
