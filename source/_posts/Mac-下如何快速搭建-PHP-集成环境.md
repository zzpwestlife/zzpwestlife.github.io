---
title: Mac 下如何快速搭建 PHP 集成环境
date: 2016-11-04
tags: mac
---

首先下载 MAMP 集成环境安装包 [MAMP官网](www.mamp.info)。免费版只支持 PHP5.6 和7.0 两个版本，需要其他版本的只能付费下载 MAMP PRO 了。

<!--more-->

下载后安装即可，非常简单

![1.png](http://upload-images.jianshu.io/upload_images/693141-764791532f1931f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![2.png](http://upload-images.jianshu.io/upload_images/693141-bf3fceb62069e051.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装完成后点击 start，右上角两个绿灯亮起就说明环境配置成功了。

![3.png](http://upload-images.jianshu.io/upload_images/693141-45f29022729c3323.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击设置，可对端口进行设置

![4.png](http://upload-images.jianshu.io/upload_images/693141-ef9d70aec1593730.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置端口后需要重新启动 MAMP。回到 MAMP 主页，点击 Open start page，可以打开浏览器，进入主页。这里可以进入 phpinfo 和 phpmyadmin。

![5.png](http://upload-images.jianshu.io/upload_images/693141-c7cd32ac6de73437.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

localhost 的目录为 ``` /Applications/MAMP/htdocs/ ```
Apache 的配置文件路径为 ``` /Applications/MAMP/conf/apache/httpd.conf ```
Apache 的虚拟主机配置文件路径为 ``` /Applications/MAMP/conf/apache/extra/httpd-vhost.conf ```

配置虚拟主机：
1. 打开 httpd.conf，取消下面这行的注释 ```# Virtual hosts Include /Applications/MAMP/conf/apache/extra/httpd-vhosts.conf ```
2. 在 httpd-vhost.conf 中配置虚拟主机
```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/Applications/MAMP/htdocs/"
    ServerName localhost
    ServerAlias
    ErrorLog "logs/dummy-host.example.com-error.log"
    CustomLog "logs/dummy-host.example.com-access.log" common
    <Directory "/Applications/MAMP/htdocs/">
        allow from all
        AllowOverride all
        Options +indexes
    </Directory>
</VirtualHost>
```
```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/Applications/MAMP/htdocs/oa"
    ServerName www.oa.cc
    ServerAlias oa.cc
    ErrorLog "logs/dummy-host.example.com-error.log"
    CustomLog "logs/dummy-host.example.com-access.log" common
    <Directory "/Applications/MAMP/htdocs/oa">
        allow from all
        AllowOverride all
        Options +indexes
    </Directory>
</VirtualHost>
```
3. 配置完成后重启 MAMP（** 注意关掉 shadowsocks 等代理软件 **）
4. 在 hosts 文件中添加 DNS 解析，即可通过域名访问本地脚本文件。有两种方式
  1. 在 Finder 上右键，选择Go to Folder...，输入```/private/etc```， 找到 hosts 文件，复制到其他地方，使用文本编辑器进行编辑，添加需要解析的行，如```127.0.0.1 www.oa.com oa.com```，保存后退出，复制，然后回到 etc 目录，粘贴，选择替换。
2. 打开终端 terminal，运行```sudo vim /private/etc/hosts```，就可以进行编辑了。编辑完成后保存即可。


