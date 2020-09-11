---
title: 使用终端卸载 Ubuntu 应用
date: 2016-11-12
tags: ubuntu
---

![](http://upload-images.jianshu.io/upload_images/693141-22922c208e54a6ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 一直以来，只是知道安装程序，而不知道怎么卸载。

<!--more-->

1. 打开终端。你将使用`apt-get`命令，这是用于管理已安装程序的通用命令。在卸载程序时，你可能需要输入管理员密码。

    当你输入密码时，密码将不会被显示。完成输入后按回车即可。
2. 浏览已安装的程序。要查看已安装的软件包列表，请输入以下命令。请注意你希望卸载的软件包的名称。

    ```
    dpkg --list | grep <programname>
    ```

3. 卸载程序和所有配置文件。在终端中输入以下命令，把<programname>替换成你希望完全移除的程序：
 
    ```
    sudo apt-get --purge remove <programname>
    ```
4. 只卸载程序。如果你移除程序但保留配置文件，请输入以下命令：

    ```
    sudo apt-get remove <programname>
    ```


