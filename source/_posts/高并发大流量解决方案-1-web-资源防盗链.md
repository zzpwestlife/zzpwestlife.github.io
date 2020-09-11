---
title: 高并发大流量解决方案 1 web 资源防盗链
date: 2018-02-15 16:30:34
tags: 高并发
---

### 1. web 资源防盗链

#### 1.  什么是防盗链？

- 盗链：是指在自己的服务器上展示一些并不在自己服务器上的内容。

   获得他人服务器上的资源地址，绕过别人的资源展示页面，直接在自己的页面上向最终用户提供此内容。

  常见的是小站盗用大站的音乐、图片、视频、文件等资源。

  通过盗链可以减轻自己服务器的负担，因为真实的空间和流量均是来自别人的服务器。

<!-- more -->

- 防盗链：防止别人通过一些技术手段绕过本站的资源展示页面，盗用本站的资源，让绕开本站资源展示页面的资源链接失效。

  可以大大减轻服务器及带宽的压力

#### 2. 防盗链的工作原理
通过 referer 或者签名，网站可以检测目标网页访问的来源网页，如果是资源文件，则可以跟踪到显示它的网页地址。
  
  一旦检测到来源不是本站即进行阻止或返回指定的页面。 
  
  通过计算签名的方式，判断请求是否合法，如果合法则显示，否则返回错误信息。

#### 3. 防盗链的实现方法
##### 1. referer 方法
 
   nginx模块 `ngx_http_referer_module` 用来阻挡来源非法的域名请求
   
   nginx 指令 `valid_referers`，全局变量 `$invalid_referer`
   
   语法： `valid_referers none | blocked | server_names | string ...;`
   
  -  `none`: 可以写也可以不写，代表 referer 是否为空，写了代表如果 referer 为空也是合法的，不写 none 表示如果 referer 为空就不合法，必须有 referer，一般可以为空；
  -  `blocked`: 表示通过代理、防火墙等途径对referer进行删除，这些值都不以 http:// 或 https:// 开头；
    -  `server_names`: referer 来源头部包含当前的 server_names，即被允许的源的列表；
  -  ` string ...`: 信任的地址，字符串或正则表达式；

示例：
``` nginx
location ~ .*\.(gif|jpg|png|flv|swf|rar|zip)$
{
	valid_referers none blocked imooc.com *.imooc.com;
	if ($invalid_referer)
	{
		#return 403;
		rewrite ^/ http://www.imooc.com/403.jpg;
	}
	expires 30d;
}
```

当访问这些扩展名结尾的文件时，会进行防盗链处理。添加 none、blocked 以及白名单（慕课网），如果来源合法，变量 `$invalid_referer` 就是0，直接跳过判断。

也可以针对目录进行防盗链，只需要修改 location 那一行
``` nginx
location /images/
{
	valid_referers none blocked imooc.com *.imooc.com;
	if ($invalid_referer)
	{
		#return 403;
		rewrite ^/ http://www.imooc.com/403.jpg;
	}
}
```

首先确认是否已安装了该模块，没有的话先安装
``` bash
$ nginx -V
nginx version: nginx/1.12.2
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx ...
```

传统防盗链存在的问题：伪造 referer。可以使用加密签名解决。

##### 2. 加密签名方法

使用第三方模块 HttpAccessKeyModule 实现 nginx 防盗链。

- `accesskey on|off` 模块开关
- `accesskey_hashmethod md5|sha-1` 签名加密方式
- `accesskey_arg` GET 参数名称
- `accesskey_signature` 加密规则

同样，首先要确认已安装该模块。

示例：
``` nginx
location ~ .*\.(gif|jpg|png|flv|swf|rar|zip)$
{
	valid_referers none blocked imooc.com *.imooc.com;
	if ($invalid_referer)
	{
		accesskey on;
		accesskey_hashmethod md5;
		accesskey_arg "your_arg";
		accesskey_signature "your_pass$remote_addr";
	}
}
```

nginx 端进行了这样的配置，php 端要相应地做相同的配置，签名才能通过。

``` php
<?php
$your_arg = md5('your_pass' . $_SERVER['REMOTE_ADDR']);
echo '<img src="address?your_arg=' . $your_arg . '">';
```

这种方法基本可以实现完全的防盗链，但是所有的资源文件的路径都要加上一个参数，对于编码来说比较麻烦。两种方式各有利弊，使用哪种方式要视具体业务及情况进行考虑。
