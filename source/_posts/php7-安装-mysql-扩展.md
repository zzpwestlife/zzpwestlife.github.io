---
title: php7 安装 mysql 扩展
date: 2017-03-06
tags: mysql
---

![](http://upload-images.jianshu.io/upload_images/693141-5a30165323bf547c.gif?imageMogr2/auto-orient/strip)
<!--more-->

PHP7全面删除Mysql扩展支持，原本的mysql_*系列函数将在mysql中不再得到支持。导致旧的项目报错，现把扩展装回，记录一下。

## 1. 首先下载[mysql扩展](http://git.php.net/?p=pecl/database/mysql.git;a=summary)

## 2. 解压
```
tar zxvf mysql.tar.gz
```
## 3. cd 进解压后的目录
```
phpize
./configure
make
```
在 modules 目录下生成了一个 mysql.so 文件。

## 4. php 配置文件添加mysql扩展

```
# 首先打印出 phpinfo()， php的配置文件在这里
Loaded Configuration File	/etc/php/7.1/fpm/php.ini
sudo vim /etc/php/7.1/fpm/php.ini
# 添加一行
extension=/path/to/mysql.so
# 重启 php-fpm
sudo /etc/init.d/php7.1-fpm restart
```


