---
title: centOS7 安装 PHP7 (updated at 2017-11-20)
date: 2017-05-06
tags: PHP
categories: PHP
description: 如果既想获得 RHEL 的高质量、高性能、高可靠性，又需要方便易用(关键是免费)的软件包更新功能，那么 Fedora Project 推出的 EPEL(Extra Packages for Enterprise Linux)正好适合你。EPEL 是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。
---

![](http://upload-images.jianshu.io/upload_images/693141-8d0e8340119e6d58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 新买了台服务器，随便倒腾，记录一下。


<!-- more -->

## 1. 安装 epel-release

### 1. 什么是epel
如果既想获得 RHEL 的高质量、高性能、高可靠性，又需要方便易用(关键是免费)的软件包更新功能，那么 Fedora Project 推出的 EPEL(Extra Packages for Enterprise Linux)正好适合你。

[EPEL](http://fedoraproject.org/wiki/EPEL) 是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。

### 2. 使用心得
1. 不用去换原来yum源，安装后会产生新repo
2. epel会有很多源地址，如果一个下不到，会去另外一个下

### 3. 安装方法

#### 1. 搜索EPEL相关的软件包

```
# yum search epel
Loaded plugins: fastestmirror
Repository base is listed more than once in the configuration
Repository updates is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository epel is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * base: mirrors.cloud.aliyuncs.com
 * epel: mirrors.cloud.aliyuncs.com
 * extras: mirrors.cloud.aliyuncs.com
 * updates: mirrors.cloud.aliyuncs.com
 * webtatic: us-east.repo.webtatic.com
================================= N/S matched: epel =================================
epel-release.noarch : Extra Packages for Enterprise Linux repository configuration
epel-rpm-macros.noarch : Extra Packages for Enterprise Linux RPM macros
python3-pkgversion-macros.noarch : Convenience macros for Fedora/EPEL Python 3
                                 : packages building

  Name and summary matches only, use "search all" for everything.
```

#### 2. 安装EPEL软件包

通过yum搜索的结果中epel-release.noarch就是我们需要的软件包。

安装可以不用包含软件包名称中点“.”后面的部分，当然包含也没有问题。点“.”后面的部分只是提供软件包适应系统架构。noarch为通用型，还有x86_64为64位的系统，i686为32位系统使用。

通过下面命令进行安装。

```
# yum install epel-release
Loaded plugins: fastestmirror
Repository base is listed more than once in the configuration
Repository updates is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository epel is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * base: mirrors.cloud.aliyuncs.com
 * epel: mirrors.cloud.aliyuncs.com
 * extras: mirrors.cloud.aliyuncs.com
 * updates: mirrors.cloud.aliyuncs.com
 * webtatic: uk.repo.webtatic.com
Package epel-release-7-9.noarch already installed and latest version
Nothing to do
```

想知道epel-release这个包包含哪些内容，就使用rpm命令查询列出。-q 表示查询，-l 标识列出软件包的所包含的文件。

```
# rpm -ql epel-release
/etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
/etc/yum.repos.d/epel-testing.repo
/etc/yum.repos.d/epel.repo
/usr/lib/systemd/system-preset/90-epel.preset
/usr/share/doc/epel-release-7
/usr/share/doc/epel-release-7/GPL
```

可以看出最主要的就是/etc/yum.repos.d/epel.repo这个文件了，默认已经启用。不确定的话可以用vi等命令查看编辑。

`enabled=1`说明已经启用

启用之后就可安装EPEL源中提供的软件了。
通过使用`yum install XXXXX`命令安装，会自动搜索所有可用的软件源，并提示可用的软件包。

## 2. 安装PHP7的rpm源

```
# rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
Retrieving https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
Preparing...                          ################################# [100%]
    package webtatic-release-7-3.noarch is already installed
```

## 3. 安装PHP7

```
# yum install php71w
```

## 4. 编译安装

### 1. 下载PHP源码包

从[这里](http://cn2.php.net/downloads.php)获取所需版本的PHP的下载地址。

```
# wget -O php7.tar.gz url/get/from/last/step
```

### 2. 解压php7

```
# tar -xvf php7.tar.gz
```

### 3. 进入php目录

```
# cd php-7.x.x
```

### 4. 安装依赖包

```
# yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel
```

### 5. 编译配置（如果出现错误，基本都是上一步的依赖文件没有安装所致）
嫌麻烦的可以从这一步起参考[PHP官方安装说明](http://php.net/manual/zh/install.unix.nginx.php)

```
# ./configure \
--prefix=/usr/local/php \
--with-config-file-path=/etc \
--enable-fpm \
--with-fpm-user=nginx  \
--with-fpm-group=nginx \
--enable-inline-optimization \
--disable-debug \
--disable-rpath \
--enable-shared  \
--enable-soap \
--with-libxml-dir \
--with-xmlrpc \
--with-openssl \
--with-mcrypt \
--with-mhash \
--with-pcre-regex \
--with-sqlite3 \
--with-zlib \
--enable-bcmath \
--with-iconv \
--with-bz2 \
--enable-calendar \
--with-curl \
--with-cdb \
--enable-dom \
--enable-exif \
--enable-fileinfo \
--enable-filter \
--with-pcre-dir \
--enable-ftp \
--with-gd \
--with-openssl-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib-dir  \
--with-freetype-dir \
--enable-gd-native-ttf \
--enable-gd-jis-conv \
--with-gettext \
--with-gmp \
--with-mhash \
--enable-json \
--enable-mbstring \
--enable-mbregex \
--enable-mbregex-backtrack \
--with-libmbfl \
--with-onig \
--enable-pdo \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-zlib-dir \
--with-pdo-sqlite \
--with-readline \
--enable-session \
--enable-shmop \
--enable-simplexml \
--enable-sockets  \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-wddx \
--with-libxml-dir \
--with-xsl \
--enable-zip \
--enable-mysqlnd-compression-support \
--with-pear \
--enable-opcache
```

这里可能会报很多错误，一个个解决即可。[这里](http://ask.apelearn.com/question/10761)有大部分的解决方法。

列出几个常见的

```
checking for png_write_image in -lpng… yes If configure fails try –with-xpm-dir=

configure: error: freetype.h not found.
Fix: Reconfigure your PHP with the following option. --with-xpm-dir=/usr

checking for png_write_image in -lpng… yes configure: error: libXpm.(a|so) not found.

Fix: yum install libXpm-devel

checking for bind_textdomain_codeset in -lc… yes checking for GNU MP support… yes configure: error: Unable to locate gmp.h

Fix: yum install gmp-devel
```

### 6. 正式安装

```
# make && make install
```

没有报错就表示安装完成。

## 4. 配置PHP

### 1. 配置环境变量

```
# vi /etc/profile
```

在末尾追加

```
PATH=$PATH:/usr/local/php/bin
export PATH
```

执行命令使得改动立即生效

```
# source /etc/profile
```

### 2. 配置php-fpm

把所需的几个配置文件放到需要的位置

```
# cp php.ini-production /etc/php.ini
# cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
# cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
# cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
# chmod +x /etc/init.d/php-fpm
```

### 3. 启动php-fpm

```
# /etc/init.d/php-fpm start
```
### 4. 查看是否已正确安装

```
# php -v
PHP 7.1.4 (cli) (built: Apr 30 2017 11:20:19) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2017 Zend Technologies
# which php
/usr/local/php/bin/php
```

## 5. 坑

### 1. Nginx Error! 

The page you are looking for is temporarily unavailable. Please try again later.

```
connect() to unix:/var/run/php-fpm/php-fpm.sock failed (2: No such file or directory) while connecting to upstream
```

参考[这里](https://www.scalescale.com/tips/nginx/php-fpm-connect-to-unixtmpphp5-fpm-sock-failed-2-no-such-file-or-directory/)的解决方案

```
# cp /etc/php-fpm.d/www.conf.rpmsave /etc/php-fpm.d/www.conf
# vi /etc/php-fpm.d/www.conf
listen 改为
listen = 127.0.0.1:9000;
```

nginx 配置文件也改
```
# vi /etc/nginx/conf.d/yoursite.com.conf

fastcgi_pass 127.0.0.1:9000;
```

重启php-fpm 和 nginx
```
# sudo /etc/init.d/php-fpm restart
# sudo systemctl restart nginx
```

上面是 TCP 的解决方案，另一种连接方式是通过 socket 套接字连接。 
Nginx连接fastcgi的方式有2种：TCP和unix domain socket, 推荐使用php-fpm的UnixSocket的方式

> Socket 可以被定义描述为两个应用通信通道的端点。一个 Socket 端点可以用 Socket 地址来描述， Socket 地址结构由 IP 地址，端口和使用协议组成（ TCP or UDP ）。http协议可以通过socket实现，socket在传输层上实现。从这个角度来说，socket介于应用层和传输层之间。但是socket作为一种进程通信机制，操作系统分配唯一一个socket号，是依赖于通信协议的，但是这个通信协议不仅仅是 tcp或udp，也可以是其它协议。


TCP是使用TCP端口连接127.0.0.1:9001
Socket是使用unix domain
socket连接套接字/dev/shm/php-cgi.sock（很多教程使用路径/tmp，而路径/dev/shm是个tmpfs，速度比磁盘快得多）

理论上，unix socket不走网络，会快些，
可是，稳定性就不那么理想了，
另外放在/tmp目录不如放在内存里面
我一般放在 /dev/shm/php-fpm.sock
放在内存读取速度快更快的

修改方法：

php-fpm listen sock 模式

#### 1. 新建套接字文件

```
touch /dev/shm/php-fpm.sock
chmod 777 /dev/shm/php-fpm.sock
# 用户组和用户名查 nginx.conf 文件
chown nginx:nginx /dev/shm/php-fpm.sock
```

#### 2. nginx 配置

```
location ~ \.php$ {
	#fastcgi_pass 127.0.0.1:9001; 
	fastcgi_pass unix:/dev/shm/php-fpm.sock;
	fastcgi_index index.php;
	# 不要忘了这一行
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	include fastcgi_params;
}
```

#### 3. php fpm conf

```
vim www.conf
;listen = 127.0.0.1:9001
listen = /dev/shm/php-fpm.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

#### 4. 问题

```
connect() to unix:/dev/shm/php-fpm.sock failed (13: Permission denied) while connecting to upstream, 
client: 192.168.121.130, server: localhost, request: "GET /index.php HTTP/1.1", 
upstream: "fastcgi://unix:/dev/shm/php-fpm.sock:", host: "192.168.121.130" 
```

确保：nginx: worker process 的用户是 nginx.conf 中的 user, Permission denied 不是一个用户导致


## 6. 参考资料
1. [How To Install Wget on CentOS](http://idroot.net/tutorials/how-to-install-wget-on-centos/)
2. [CentOS怎样强制卸载PHP以及自定义安装PHP](http://blog.csdn.net/21aspnet/article/details/6581618)
3. [CentOS7 安装 PHP7最新版](http://www.jianshu.com/p/246ffcd5e77d)
4. [PHP编译过程中常见错误信息的解决方法](http://lyp.cn/350_how-to-fix-php-compile-errors)
5. [PHP编译安装时常见错误解决办法，php编译常见错误](http://ask.apelearn.com/question/10761)
6. [PHP-FPM connect() to unix:/tmp/php5-fpm.sock failed (2: No such file or directory)](https://www.scalescale.com/tips/nginx/php-fpm-connect-to-unixtmpphp5-fpm-sock-failed-2-no-such-file-or-directory/)

