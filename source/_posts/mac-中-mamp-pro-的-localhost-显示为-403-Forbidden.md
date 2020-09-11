---
title: mac 中 mamp-pro 的 localhost 显示为 403 Forbidden
date: 2016-11-15
tags: mac
description: 解决方法很简单，在 mamp pro 中，依次找到Hosts > Extended > Indexes，勾选 Indexes，重启即可。
---

![](http://upload-images.jianshu.io/upload_images/693141-04ef97fb9985702e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!--more-->
解决方法很简单，在 mamp pro 中，依次找到 

Hosts > Extended > Indexes，勾选 Indexes，重启即可。

另外一个报错可能是 500 服务器错误，解决方法如下：

只需要配置php.ini即可。
在php的安装目录中找到php.ini文件并打开，找到display_errors，
默认情况下是display_errors = Off，把Off修改为On，保存关闭文件,然后重启apache。


