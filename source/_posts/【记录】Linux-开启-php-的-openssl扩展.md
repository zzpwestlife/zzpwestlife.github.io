---
title: 【记录】Linux 开启 php 的 openssl 扩展
date: 2017-01-31
tags: openssl
description: php 版本升级到7.1.3后，发现 mcrypt加密算法不能用了。推荐的替代方案就是openssl，现记录一下其安装过程。
---
![](http://upload-images.jianshu.io/upload_images/693141-d7d7e6eb62a8ce9b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> php 版本升级到7.1.3后，发现 mcrypt加密算法不能用了。推荐的替代方案就是openssl，现记录一下其安装过程。


![](http://upload-images.jianshu.io/upload_images/693141-b96962a577e0f494.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!-- more -->

### 1. 下载php源码包

首先确定使用的php版本，`php -v`，也可以在`phpinfo()`中查看。

![](http://upload-images.jianshu.io/upload_images/693141-f2c96ade456c299c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图，我的版本是7.1.3。

下载并解压，进入PHP的openssl扩展模块目录

```
$ wget http://mirrors.sohu.com/php/php-7.1.3.tar.gz
$ tar zxvf php-7.1.3.tar.gz
$ cd php-7.1.3/ext/openssl
```

编译安装

```
# $ which phpize 找到phpize应用位置
/usr/bin/phpize
# 然后执行
$ /usr/bin/phpize
# 执行后，发现错误 无法找到config.m4 ，config0.m4就是config.m4。直接重命名
$ cp config0.m4 config.m4
# 重新执行phpize
$ /usr/bin/phpize
# $ which php-config 找到php-config应用位置
/usr/bin/php-config
# 然后执行
$ ./configure --with-openssl --with-php-config=/usr/bin/php-config
$ make
$ make test
$ sudo make install
```

安装完成后，会返回一个.so文件（`openssl.so`）的目录。在此目录下把`openssl.so` 文件拷贝到你在`php.ini` 中指定的 `extension_dir` 下（在`php.ini`文件中查找：`extension_dir =` 或者打印出`phpinfo()`），我这里的目录是 `/usr/lib/php/20160303`

![](http://upload-images.jianshu.io/upload_images/693141-7b85fea403ac77d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在php配置文件添加 openssl 扩展

```
$ /etc/php/7.1/mods-available
$ touch openssl.ini
$ sudo vim openssl.ini
```

文件内容为

```
; configuration for php openssl module
; priority=20
extension=openssl.so
```

重启

```
sudo /etc/init.d/php7.1-fpm restart
sudo service nginx restart
```

再次打印出`phpinfo()`，可以看到openssl相关的信息。

![](http://upload-images.jianshu.io/upload_images/693141-d8737c97f385e984.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


