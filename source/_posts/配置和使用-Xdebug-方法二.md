---
title: 配置和使用-Xdebug-方法二
date: 2017-02-04
tags: xdebug
---

![](http://upload-images.jianshu.io/upload_images/693141-33e6d9872c86262a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->
# 前提

基本配置参见前一篇[《配置和使用 Xdebug (Laravel + Homestead + Xdebug)》](http://www.jianshu.com/p/3a3db0359bc7)

# 不同
xdebug.so 文件的内容改为：
```
zend_extension="/usr/lib/php/20151012/xdebug.so"
xdebug.idekey="PHPSTORM"
xdebug.profiler_append = 0
xdebug.profiler_enable = off
xdebug.profiler_enable_trigger = off
xdebug.profiler_output_dir = "/tmp"
xdebug.profiler_output_name = "cachegrind.out.%t-%s"
xdebug.remote_enable = on
xdebug.remote_handler = "dbgp"
xdebug.remote_host = "127.0.0.1"
xdebug.trace_output_dir = "/tmp"
xdebug.remote_connect_back = On
xdebug.remote_port=9000
xdebug.auto_trace=off
xdebug.default_enable=on
xdebug.collect_params=4
xdebug.collect_return=on
xdebug.show_exception_trace=On
xdebug.max_nesting_level = 10000
```

# 安装 Chrome 的 Xdebug Helper 插件

# 使用
1. 打断点
2. 点击 phpstorm 的电话图标，开始监听
3. Chrome 中，enable Xdebug Helper 插件
4. 点击页面至断点所在位置，开始调试

切换项目以及操作不当等可能导致PHP服务崩溃或系统崩溃，此时需重启PHP服务或系统，很蛋疼。
个人更喜欢方法一
以上配置方法一也可以完美运行


