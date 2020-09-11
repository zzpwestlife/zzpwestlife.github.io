---
title: bd：快速返回某级父目录而不用冗余地输入 “cd ..”
date: 2017-05-20 10:27:37
tags: Linux
categories: Linux
---

> 在 Linux 系统上通过命令行切换文件夹时，为了回到父目录（长路径），我们通常会重复输入 cd 命令（`cd ../../..`），直到进入感兴趣的目录。

转自 [Linux 中国 bd：快速返回某级父目录而不用冗余地输入 “cd ..”](https://linux.cn/article-8491-1.html)，有删改。

建议阅读： [Autojump - 一个快速浏览 Linux 文件系统的高级 “cd” 命令](https://linux.cn/article-5983-1.html)

<!--more-->

在 Linux 系统上通过命令行切换文件夹时，为了回到父目录（长路径），我们通常会重复输入 cd 命令（`cd ../../..`），直到进入感兴趣的目录。

对于经验丰富的 Linux 用户或需要进行各种不同任务的系统管理员而言，这可能非常乏味，因此希望在操作系统时有一个快捷方式来简化工作。

bd 是用于切换文件夹的便利工具，它可以使你快速返回到父目录，而不必重复键入 `cd ../../..` 。 你可以可靠地将其与其他 Linux 命令组合以执行几个日常操作。

## 安装 [bd](https://github.com/vigneshwaranr/bd)

```
wget --no-check-certificate -O /usr/local/bin/bd https://raw.github.com/vigneshwaranr/bd/master/bd
chmod +rx /usr/local/bin/bd
echo 'alias bd=". bd -si"' >> ~/.bashrc
source ~/.bashrc
```

**注意**：如果 shell 使用的是 zsh，后面两步的 `.bashrc` 要替换成 `.zshrc`


## 使用

使用方法很简单。

假定当前目录为`/home/user/project/src/org/main/site/utils/file/reader/whatever`，想要快速跳转至 site 目录，只需输入 `bd site` 即可。甚至只需输入目前的前几个字母都可以，比如 `bd si`。

配合 autojump 使用，爽歪歪。










