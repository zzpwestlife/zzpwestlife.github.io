---
title: mac 下搭建 laravel 运行环境
date: 2016-12-23
tags: laravel
---

![](http://upload-images.jianshu.io/upload_images/693141-e63645d2638a654a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->
1. 安装 composer

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"```
可能出现 openssl 相关的错误，在之前的文章中已有解决方案。

执行第一条命令下载下来的 composer-setup.php 脚本将简单的检测 php.ini 中的参数设置，如果某些参数未正确设置则会给出警告；
然后下载最新版本的 composer.phar 文件到当前目录。
上述 3 条命令的作用依次是：
下载安装脚本（composer-setup.php）到当前目录。
检验文件的 hash 值。
执行安装过程。
删除安装脚本 -- composer-setup.php 。
2. 添加 composer 至全局变量
```mv composer.phar /usr/local/bin/composer```
现在输入 ```composer -V ```试试，就可以显示版本号了。

3.使用 composer 下载 laravel 安装包
```composer global require "laravel/installer=~1.1"```
然后把 ~/.composer/vendor/bin 路径放置于您的 PATH 里， 这样 laravel 执行文件就会存在你的系统。
具体方法为：cd 到/Users/ZouZhipeng，即.bash_profile所在的目录
```sudo vim .bash_profile```
 添加一行
```export PATH="/Users/ZouZhipeng/.composer/vendor/bin:$PATH"```


