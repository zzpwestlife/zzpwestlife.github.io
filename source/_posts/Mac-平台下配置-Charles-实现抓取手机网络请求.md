---
title: Mac 平台下配置 Charles 实现抓取手机网络请求
date: 2017-05-27 21:18:08
tags: Charles
Categories: Charles
---

> 在测试app的时候，往往需要去定位问题，找到所抛出的请求是否异常，Mac 平台下课通过 Charles 来实现，那么如何抓到手机抛出的请求呢？

前提： Mac 和 手机处在同一局域网下

<!--more-->
# 1. 配置 Charles 实现 HTTP 请求的抓取

## 1. 设置 Charles 代理

Charles -> Proxy -> Proxy Settings -> Proxies, 在 Http Proxy  的 Port 中填写代理的端口，默认为 8888.

![](http://omp48p40q.bkt.clouddn.com/17-5-27/13486922.jpg)

![](http://omp48p40q.bkt.clouddn.com/17-5-27/63870331.jpg)

## 2. 获取 Mac 当前 IP

方法有很多种，介绍一种最简单的

按住 option 键，点击右上角的 WiFi 图标即可

![](http://omp48p40q.bkt.clouddn.com/17-5-27/18622996.jpg)

## 3. 设置手机 HTTP 代理

设置 -> 无线局域网 -> 与 Mac 同一 WiFi -> 点击右侧的信息 -> 页面最下面设置 HTTP 代理，服务器和端口在前两部中已获得，填入即可。

![](http://omp48p40q.bkt.clouddn.com/17-5-27/54533762.jpg)

> <font color=red>注意： 在不使用的时候需要将手机中的 HTTP 代理关闭，否则 mac 关机或者关闭软件后，手机无法正常上网！</font>

# 2. 配置 Charles 实现 HTTPS 请求的抓取

如果不进行下面的设置， https 的 reqeust 和 response 都是乱码，设置完之后 https 就可以抓包了。

## 1. Mac 端安装证书

点击 Charles 菜单的 help -> SSL -> proxying -> install charles root certificate

![](http://omp48p40q.bkt.clouddn.com/17-5-27/98451153.jpg)

## 2. 从 Keychains 找到刚安装的证书，并选择信任

安装完成后 keychains 会自动弹出。选择信任后，需要输入 Mac 的登录密码才能保存。

![](http://omp48p40q.bkt.clouddn.com/17-5-27/74007718.jpg)

## 3. 手机端安装证书

Safari 中输入 chls.pro/ssl， 按提示一步步操作即可。此步需要输入手机密码。

![](http://omp48p40q.bkt.clouddn.com/17-5-27/20454656.jpg)

> <font color=red>注意： 对于 iOS 10.3 及以后的版本，安装完成后并不算结束，还需要一步设置：设置 -> 通用 -> 关于本机 -> 证书信任设置，找到 Charles 的证书，选择信任。</font>

## 4. Charles 设置

Charles -> Proxy -> SSL Proxy Setting

![](http://omp48p40q.bkt.clouddn.com/17-5-27/30483139.jpg)

在弹出的窗口勾选 Enable SSL Proxying, 选择 Add，在弹窗中填入要抓取的域名和端口，如 API，baidu.com， Port: 443

![](http://omp48p40q.bkt.clouddn.com/17-5-27/62707963.jpg)

配置完成，在手机端打开一个 https 站点试试。

![](http://omp48p40q.bkt.clouddn.com/17-5-27/22162539.jpg)

Https 请求的 response 的 content 不再是乱码，可以愉快的玩耍了。

不用的时候最好还是关掉，需要的再打开就好。





