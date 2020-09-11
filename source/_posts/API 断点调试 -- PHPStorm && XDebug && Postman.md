---
title: API 断点调试 -- PHPStorm && XDebug && Postman
date: 2019-02-16 12:45:02
tags: xdebug
---

折腾了好久，记录一下，方便以后查阅。

### 环境
- MacOS Majave version 10.14.2
- phpstorm 2018.2.2
- Docker version 18.09.1, build 4c52b90
- docker-compose version 1.23.2, build 1110ad01
- postman 6.6.1
- php 7.1.23
- xdebug v2.6.1

### 安装
#### 1. 配置文件

> docker-compose.yml

```
# 本机 80 端口被占用，随便改一个其他未使用端口
version: '3'
services:
  fpm:
    build: .
    ports:
    - "9111:80"
    stdin_open: true
    tty: true
    volumes:
    - .:/var/www/html
    restart: always
    command: "sh -c 'nginx && php-fpm'"
```
 
> Dockerfile

```
FROM php:7.1-fpm

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

RUN echo 'date.timezone=PRC' > /usr/local/etc/php/conf.d/timezone.ini

# Install modules
RUN apt-get update && apt-get install -y \
        git \
        curl \
        openssl \
        wget \
        vim \
        procps \
        libssl-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libicu-dev \
        libpcre3 libpcre3-dev \
        zlib1g-dev \
        gnupg2 \
             --no-install-recommends

# ########### nginx ################
RUN wget http://nginx.org/download/nginx-1.12.0.tar.gz -O nginx.tar.gz \
    && mkdir -p nginx \
    && tar -xf nginx.tar.gz -C nginx --strip-components=1 \
    && rm nginx.tar.gz

RUN cd nginx && ./configure \
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --modules-path=/usr/lib/nginx/modules \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --http-client-body-temp-path=/var/cache/nginx/client_temp \
    --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
    --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
    --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
    --user=www-data \
    --group=www-data \
    --with-compat \
    --with-file-aio \
    --with-threads \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_mp4_module \
    --with-http_random_index_module \
    --with-http_realip_module \
    --with-http_secure_link_module \
    --with-http_slice_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-mail \
    --with-mail_ssl_module \
    --with-stream \
    --with-stream_realip_module \
    --with-stream_ssl_module \
    --with-stream_ssl_preread_module \
    --with-cc-opt='-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' \
    --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' \
    && make \
    && make install \
    && mkdir -p /var/cache/nginx/


# Install composer && global asset plugin
ENV COMPOSER_HOME /root/.composer
ENV PATH /root/.composer/vendor/bin:$PATH
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

#install extension
RUN docker-php-ext-install zip \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install intl \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install bcmath

#install gd
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

# redis
RUN wget http://pecl.php.net/get/redis-3.1.6.tgz \
    && pecl install redis-3.1.6.tgz \
    && rm -f redis-3.1.6.tgz \
    && docker-php-ext-enable redis

# amqp
RUN apt install librabbitmq-dev -y

RUN wget https://github.com/alanxz/rabbitmq-c/releases/download/v0.8.0/rabbitmq-c-0.8.0.tar.gz -O rabbitmq.tar.gz \
    && mkdir -p rabbitmq \
    && tar -xf rabbitmq.tar.gz -C rabbitmq --strip-components=1 \
    && rm rabbitmq.tar.gz \
    && cd rabbitmq \
    && ./configure --prefix=/usr/local/rabbitmq-dev \
    && make \
    && make install \
    && cd .. \
    && rm -rf rabbitmq

RUN wget http://pecl.php.net/get/amqp-1.9.3.tgz -O amqp.tar.gz \
    && mkdir -p amqp \
    && tar -xf amqp.tar.gz -C amqp --strip-components=1 \
    && rm amqp.tar.gz \
    && cd amqp \
    && phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config  --with-amqp --with-librabbitmq-dir=/usr/local/rabbitmq-dev \
    && make -j$(nproc) \
    && make install \
    && docker-php-ext-enable amqp

# tideways
RUN git clone https://github.com/tideways/php-profiler-extension.git \
        && cd php-profiler-extension \ tideways_xhprof --strip-components=1 \
        && phpize \
        && ./configure \
        && make \
        && make install

# xdebug 重点看这里即可
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port=9211" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_host=192.168.0.103" >> /usr/local/etc/php/conf.d/xdebug.ini

RUN apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    && rm -rf /tmp/*


WORKDIR /var/www/html
COPY ./nginx.conf /etc/nginx/nginx.conf
COPY ./local.ini /usr/local/php/conf/

RUN echo 'date.timezone=PRC' > /usr/local/etc/php/conf.d/timezone.ini

RUN usermod -u 1000 www-data

EXPOSE 80 443
CMD ["sh", '-c', 'nginx && php-fpm']
```

> <font color='red'>重点关注 xdebug 一段的安装及配置</font>，相当于

```
; Enable xdebug extension module
zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)
xdebug.idekey = "PHPSTORM"
; 因为DBGp调试代理是可以做调试分发的，所以，定义一个IDEKEY，目的是让调试器知道，是不是发给自己的请求

xdebug.remote_enable = 1
; 设置为1的时候，会先参数链接到remote_host和remote_port指定的调试器端口，如果连接不上，就继续执行，类似设置>了0

xdebug.remote_mode = "req"
xdebug.remote_handler = "dbgp"
; 也就是调试走哪种协议，有老的PHP3协议，也有GDB协议，DBGP是当前的默认协议，也是当前主流支持的协议

xdebug.remote_connect_back = 0
; 如果为1，xdebug会通过$_SERVER[‘REMOTE_ADDR’]变量，向发起HTTP请求的客户端发起链接，和remote_host功能类似>，但是优先级比remote_host高，所以，设置了这个选项就会忽略remote_host，由于我的运行时服务器不能主动链接IDE所>在PC，所以不能开启自动回连模式(内网访问外网的网页，也不能开启)

# 刚开始这里配置错了，怎么样都不行。。。
xdebug.remote_host = "192.168.0.103"
; 默认是主机，如果服务器可以直接你的本地电脑，可以直接填写本机ip，我这里使用xshell做tcp转发

xdebug.remote_port = 9211
; 默认端口是9000，由于端口被fpm占用，我修改为9211，建议没有端口冲突时不修改

xdebug.remote_autostart = 0
; 这个为1，会忽略COOKIE或者POST/GET中带的XDEBUG_SESSION参数，不管有没有，都会启动调试，所以，还是设置为0比较好，默认0
```

> local.ini

```
post_max_size = 256m
upload_max_filesize = 256m
```

> nginx.conf

```
user  www-data;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/json;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    gzip  on;

    ## PHP-FPM Servers ##
    upstream php-fpm {
        server localhost:9000;
    }

    server {
        listen       80;
        server_name  localhost;
        root   "/var/www/html/";

        client_max_body_size 32m;

        location / {
            index  index.php;
            if (!-e $request_filename){
                rewrite ^/(.*) /index.php last;
            }
        }

        location ~ ^(.+\.php)(.*)$ {
            fastcgi_index        index.php;
            fastcgi_split_path_info ^(.+\.php)(.*)$;
            fastcgi_param        SCRIPT_FILENAME        $document_root$fastcgi_script_name;
            fastcgi_param        PATH_INFO              $fastcgi_path_info;
            fastcgi_param        PATH_TRANSLATED        $document_root$fastcgi_path_info;
            fastcgi_pass php-fpm;
            client_max_body_size 100m;
            include        fastcgi_params;
            add_header Access-Control-Allow-Origin "*";
        }
    }
}
```

#### 2. 安装及验证

> docker 环境

```
# 安装
docker-compose build
# 启动
docker-compose up -d
# 查看容器状态
docker-compose ps
# 进入容器
docker-compose exec fpm bash

# 查看
php -v
PHP 7.1.23 (cli) (built: Oct 16 2018 01:08:49) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2018 Zend Technologies
    with Xdebug v2.6.1, Copyright (c) 2002-2018, by Derick Rethans
# 可以看到PHP已正确安装，且 xdebug 扩展已开启

# 新建 index.php 文件，在宿主机浏览器直接访问，得到结果，表示 nginx 已正确配置
# 我这里 docker 虚拟机配置的端口为 9111
curl http://localhost:9111
```

> 配置 PHPstorm

首先配置 PHPstorm 使用的 CLI 解释器为 docker 虚拟机内的 PHP

![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-195714.jpg)

![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-195838.jpg)

配置 xdebug 端口
![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-200745.jpg)
    
Run -> Edit Configuration 或者 或者点击 PHPstorm 右上角的 Edit Configuration

![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-201139.jpg)

![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-201423.jpg)

> 浏览器调试，安装对应的扩展即可，略

> postman 断点调试

1. 选择刚才的配置，设置断点，开启小电话
    ![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-201724.jpg)
2. 在指定域名（如 localhost）的 cookie 中添加一次 XDEBUG_SESSION=PHPSTORM
    ![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-201904.jpg)
    ![](https://zzpwestlife.oss-cn-beijing.aliyuncs.com/blog/Jietu20190216-201952.jpg)
3. 访问，即可自动跳回至 PHPstorm 的断点处，可以愉快地调试了。

下一步，记录调试的流程、快捷键、技巧等，敬请期待。


