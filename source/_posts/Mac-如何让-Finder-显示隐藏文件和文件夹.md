---
title: Mac 如何让 Finder 显示隐藏文件和文件夹
date: 2017-07-15 20:37:35
tags: Mac
categories: Mac
---

> 原文地址：https://sspai.com/post/26273

## 如何让 Finder 显示隐藏文件和文件夹

第一步：打开「终端」应用程序。

第二步：输入如下命令：

```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```

第三步：按下「Return」键确认。

<!-- more -->

现在你将会在 Finder 窗口中看到那些隐藏的文件和文件夹了。

如果你想再次隐藏原本的隐藏文件和文件夹的话，将上述命令替换成

```
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```

即可。

注：该命令适用于 OS X Mavericks 和 OS X Yosemite 系统。对于还在使用 OS X Mountain Lion 或是更早版本的系统的 Mac 用户来说，命令需要稍微变化一下。


