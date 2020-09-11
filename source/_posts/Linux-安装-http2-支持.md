---
title: Linux 安装 http2 支持
date: 2017-01-17
tags: http2
description: HTTP 2.0即超文本传输协议 2.0，是下一代HTTP协议.
---

![](http://upload-images.jianshu.io/upload_images/693141-ffa57dd27771a53c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!--more-->

1. 前提：服务器中已有 git，如果没有，输入下面的命令安装
    ```
    sudo apt-get install -y tmux curl vim wget htop git
    ```
2. curl请求一个需使用 http2 的网址，查看响应结果，为http1.1，说明暂时还不支持http2
    ```
        vagrant@homestead:~$ curl -I https://nghttp2.org/
    HTTP/1.1 200 OK
    Date: Tue, 28 Mar 2017 04:58:27 GMT
    Content-Type: text/html
    Last-Modified: Mon, 27 Mar 2017 14:39:24 GMT
    Etag: "58d9241c-19ff"
    Accept-Ranges: bytes
    Content-Length: 6655
    X-Backend-Header-Rtt: 0.001072
    Strict-Transport-Security: max-age=31536000
    Server: nghttpx
    Via: 2 nghttpx
    x-frame-options: SAMEORIGIN
    x-xss-protection: 1; mode=block
    x-content-type-options: nosniff
    ```
    如果强制使用http2请求，无法得到响应
    ```
    vagrant@homestead:~$ curl --http2 -I https://nghttp2.org/
    curl: (1) Unsupported protocol
    ```
3. 安装 nghttp2
    ```
    # Get build requirements
    # Some of these are used for the Python bindings
    # this package also installs
    sudo apt-get install g++ make binutils autoconf automake autotools-dev libtool pkg-config \
    zlib1g-dev libcunit1-dev libssl-dev libxml2-dev       libev-dev libevent-dev libjansson-dev \
    libjemalloc-dev cython python3-dev python-setuptools
    
    # Build nghttp2 from source
    git clone https://github.com/tatsuhiro-t/nghttp2.git
    cd nghttp2
    autoreconf -i
    automake
    autoconf
    ./configure
    make
    sudo make install
    ```
4. 升级最新版的 curl，[这里](https://curl.haxx.se/download.html)查看curl版本
    ```
    cd ~
    sudo apt-get build-dep curl
    wget http://curl.haxx.se/download/curl-7.xx.0.tar.bz2
    tar -xvjf curl-7.xx.0.tar.bz2
    cd curl-7.xx.0
    ./configure --with-nghttp2=/usr/local --with-ssl
    make
    sudo make install
    sudo ldconfig
    ```

5. 尝试再次连接
    ```
    # Try this out first
    curl --http2 -I nghttp2.org
    
    # If you get errors, try setting this constant
    # to tell curl where to find shared libraries
    LD_LIBRARY_PATH=/usr/local/lib /usr/local/bin/curl --http2 -I nghttp2.org
    ```

6. 连接
    ```
    vagrant@homestead:~/curl-7.xx.0$ LD_LIBRARY_PATH=/usr/local/lib /usr/local/bin/curl --http2 -k -I -H "Host: example.com" https://localhost
    HTTP/2.0 403
    server:nginx/1.11.9
    date:Tue, 28 Mar 2017 05:47:10 GMT
    content-type:text/html; charset=utf-8
    content-length:169
    ```
7. 打印出phpinfo
curl 的http2 显示 yes，表示成功。否则需要手动安装php的http扩展。

![](http://upload-images.jianshu.io/upload_images/693141-3e7fd6d43ffe52c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


