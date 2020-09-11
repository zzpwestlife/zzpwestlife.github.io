---
title: nginx 入门到实践 慕课网
date: 2018-02-18 17:11:16
tags: nginx
---


nginx 是一个开源且高性能、可靠的HTTP中间件、代理服务。

安装 `yum install nginx`

<!--more-->

### 1. 安装目录

```
[root@iz2ze4kikm9076bxac1noqz opt]# rpm -ql nginx
/etc/logrotate.d/nginx
/etc/nginx
/etc/nginx/conf.d
/etc/nginx/conf.d/default.conf
/etc/nginx/fastcgi_params
/etc/nginx/koi-utf
/etc/nginx/koi-win
/etc/nginx/mime.types
/etc/nginx/modules
/etc/nginx/nginx.conf
/etc/nginx/scgi_params
/etc/nginx/uwsgi_params
/etc/nginx/win-utf
/etc/sysconfig/nginx
/etc/sysconfig/nginx-debug
/usr/lib/systemd/system/nginx-debug.service
/usr/lib/systemd/system/nginx.service
/usr/lib64/nginx
/usr/lib64/nginx/modules
/usr/libexec/initscripts/legacy-actions/nginx
/usr/libexec/initscripts/legacy-actions/nginx/check-reload
/usr/libexec/initscripts/legacy-actions/nginx/upgrade
/usr/sbin/nginx
/usr/sbin/nginx-debug
/usr/share/doc/nginx-1.12.2
/usr/share/doc/nginx-1.12.2/COPYRIGHT
/usr/share/man/man8/nginx.8.gz
/usr/share/nginx
/usr/share/nginx/html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/index.html
/var/cache/nginx
/var/log/nginx
```


| 路径                                                                                                                                                         | 类型 |             作用                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | ------------------------------- |
| `/etc/logrotate.d/nginx`                                                                                                                                   |  配置文件 | nginx日志轮转，用于logrotate服务的日志切割 |
| `/etc/nginx` <br/> `/etc/nginx/nginx.conf` <br/> `/etc/nginx/conf.d` <br/> `/etc/nginx/conf.d/default.conf`                                                |  目录、配置文件 | nginx主配置文件 |
| `/etc/nginx/fastcgi_params` <br/> `/etc/nginx/uwsgi_params` <br/> `/etc/nginx/scgi_params`                                                                 |  配置文件 | cgi配置相关，fastcgi配置 |
| `/etc/nginx/koi-utf` <br/> `/etc/nginx/koi-win` <br/> `/etc/nginx/win-utf `                                                                                |  配置文件 | 编码转换映射转化文件 很少用到 |
| `/etc/nginx/mime.types`                                                                                                                                    |  配置文件 | 设置HTTP协议的Content-Type与扩展名对应关系 |
| `/etc/sysconfig/nginx` <br/> `/etc/sysconfig/nginx-debug` <br/> `/usr/lib/system/system/nginx-debug.service` <br/> `/usr/lib/systemd/system/nginx.service` |  配置文件 | 用于配置出系统守护进程管理器管理方式，取代旧的init.d的脚本管理方式 |
| `/usr/lib64/nginx/modules` <br/> `/etc/nginx/modules`                                                                                                      |  目录 | nginx 模块目录 |
| `/usr/sbin/nginx` <br/> `/usr/sbin/nginx-debug`                                                                                                            |  命令 | nginx 服务的启动管理终端命令， nginx 服务的启动、关闭、配置、管理 |
| `/usr/share/doc/nginx-1.12.2` <br/> `/usr/share/doc/nginx-1.12.2/COPYRIGHT` <br/> `/usr/share/man/man8/nginx.8.gz`                                         |  文件、目录 | nginx的手册和帮助文件 |
| `/var/cache/nginx`                                                                                                                                         | 目录 | nginx的缓存目录 |
| `/var/log/nginx`                                                                                                                                           | 目录 | nginx的日志目录 |

### 2. 编译参数

查看编译时所用到的参数 `nginx -V` 大写

```
[root@iz2ze4kikm9076bxac1noqz opt]# nginx -V
nginx version: nginx/1.12.2
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'
```

| 路径                                                                                                                                                                                                                                                                                                                                     |             作用                |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| `--prefix=/etc/nginx` <br/> `--sbin-path=/usr/sbin/nginx`  <br/> `--modules-path=/usr/lib64/nginx/modules` <br/> `--conf-path=/etc/nginx/nginx.conf` <br/> `--error-log-path=/var/log/nginx/error.log` <br/> `--http-log-path=/var/log/nginx/access.log` <br/> `--pid-path=/var/run/nginx.pid` <br/> `--lock-path=/var/run/nginx.lock` | 安装目的目录或路径 nginx 基础路径 |
| `--http-client-body-temp-path=/var/cache/nginx/client_temp` <br/> `--http-proxy-temp-path=/var/cache/nginx/proxy_temp`  <br/> `--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp` <br/> `--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp` <br/> `--http-scgi-temp-path=/var/cache/nginx/scgi_temp`                             | 执行对应模块时，nginx所保留的临时文件 |
| `--user=nginx --group=nginx`                                                                                                                                                                                                                                                                                                           | 设定nginx进程启动的用户和用户组 |
| `--with-cc-opt= ... `                                                                                                                                                                                                                                                                                                                  | 设置额外的参数将被添加到CFLAGS变量 不需要了解 |
| `--with-ld-opt= ... `                                                                                                                                                                                                                                                                                                                  | 设置附件的参数，链接系统库 |


### 3. 基本配置语法 nginx.conf default.conf

#### 全局性、服务级别配置

|     配置     |             作用                |
| ------------ | ------------------------------- |
|user|设置nginx服务的系统使用用户，一般为nginx，不用修改|
|worker_processes   | 工作进程数，一般和cpu核数保持一致即可|
|error_log|nginx错误日志|
|pid|nginx服务启动时的pid|

#### 事件模块配置 events

|     配置     |             作用                |
| ------------ | ------------------------------- |
|worker_connections|每个进程运行的最大连接数 最大65535，一般10000左右最多 |
|use|工作进程数，内核模型|

#### default.conf 配置

``` nginx
server {
    listen       80; # 监听的端口号
    server_name  localhost; # 服务的域名

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    
    # 首页路径或子路径
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    # 错误页面
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```


























