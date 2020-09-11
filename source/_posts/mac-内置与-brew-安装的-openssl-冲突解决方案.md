---
title: mac 内置 brew 安装的 openssl 冲突解决方案
date: 2016-10-08
tags: openssl
description: mac 系统自带的 openssl 信息如下： Version 0.9.8 - 位于 /usr/bin/openssl； 
---

![](http://upload-images.jianshu.io/upload_images/693141-4d7763bad2f0d9ee.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->
mac 系统自带的 openssl 信息如下：
```Version 0.9.8 - 位于 /usr/bin/openssl```；
brew 安装的 openssl 信息如下：
```Version 1.0.2h_1 - 位于 /usr/local/Cellar/openssl/1.0.2h_1/bin/openssl```

terminal 中输入
```which openssl```或者```openssl version```
可以看出，都是指向系统默认安装的，现在要做的就是把这个指向改为brew 安装的新版本，也就是 PHP 要进行编译所使用的版本。

方法：建立一个两版本之间的软链
```ln -s /usr/local/Cellar/openssl/1.0.2h_1/bin/openssl /usr/bin/openssl```
如果上述操作无法执行，只能将原来的 openssl 先重命名，然后建立软链，但这样可能带来稳定性问题。

```mv /usr/bin/openssl /usr/bin/openssl_OLD
ln -s /usr/local/Cellar/openssl/1.0.2h_1/bin/openssl /usr/bin/openssl```
但我这里上述两种方式都报错，加 sudo，改权限777也没用。
```Operation not permitted```

###寻求其他解决方案
待添加，一个思路是更改环境变量的路径顺序


