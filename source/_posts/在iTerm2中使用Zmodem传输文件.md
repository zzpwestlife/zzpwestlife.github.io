---
title: 在iTerm2中使用Zmodem传输文件
date: 2017-05-12 10:23:48
tags: Linux
categories: Linux
---

> rz,sz命令传输文件，比使用scp要方便得多，特别是在图形界面打开终端，SSH登陆到远程机器需要传输文件的时候。但是MacOS里Terminal.app并不支持Zmodel传输。好在iTerm2具备较强的扩展性可以通过简单的配置支持Zmodem传输。

<!--more-->

Zmodem 是跨平台的文件传输协议，可以很方便的在不同的操作系统之间接传输文件。lzrsz 是该协议的实现方式：https://ohse.de/uwe/software/lrzsz.html 。安装后，在 Mac 的 ITerm2 中用 SSH 登陆远程的 Linux 主机，然后用 rz 、sz 命令传输文件。

## 1. 安装 lrzsz

在 Ubuntu 中安装:

```
$ sudo apt-get install lrzsz
```

在 CentOS 中安装:

```
$ yum -y install lrzsz
```

在 Mac 中安装：

```
$ brew install lrzsz
```

## 2. 下载脚本文件

从[这里](https://github.com/mmastrac/iterm2-zmodem)下载。复制到 /usr/local/bin/，并增加执行权限。

```
$ git clone https://github.com/mmastrac/iterm2-zmodem.git
$ cp iterm2-zmodem/iterm2-send-zmodem.sh /usr/local/bin/iterm2-send-zmodem.sh
$ cp iterm2-zmodem/iterm2-recv-zmodem.sh /usr/local/bin/iterm2-recv-zmodem.sh
$ chmod +x /usr/local/bin/iterm2-send-zmodem.sh
$ chmod +x /usr/local/bin/iterm2-recv-zmodem.sh
```

## 3. 配置 iTerm2

iTerm2 -> Profiles -> Default ->Advanced -> Triggers -> edit

```
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh
Instant: checked

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
Instant: checked
```

## 4. 使用

发送文件：

1. 登录服务器
2. `$ rz`
3. 在弹窗中从本地选择文件
4. 确定，等待

接收文件：

1. 登录服务器
2. `$ sz filename1 filename2 ... filenameN`
3. 在弹窗中选择接收的本地目录
4. 确定，等待