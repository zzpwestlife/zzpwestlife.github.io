---
title: Laravel 项目中 远程把图片下载到本地
date: 2017-05-15
tags: Laravel
categories: Laravel
---

>如今，开发应用时，我们会从不断增多的大量专用组件中选择合适的。既然已经有了 [`guzzlehttp/guzzle` 组件](https://github.com/guzzle/guzzle)，为什么还要浪费时间自己编写处理HTTP请求和响应库呢？

<!--more-->

最近在做第三方登录，用户的头像处理成了一个问题。原来用户系统中的头像都是以文件的形式保存在我们自己的服务器中。

第三方账号的头像是以 url 的形式传入后台，考虑先通过后台将图片下载在服务器，重命名后存表。这样就与之前的用户系统一致，不用改代码。


## 安装 guzzle 组件

安装 guzzle 组件需要先安装 composer

```
# Install Composer
curl -sS https://getcomposer.org/installer | php
```
使用 composer 安装 guzzle

```
php composer.phar require guzzlehttp/guzzle
# 或者
 composer require guzzlehttp/guzzle
```

## 用法

[guzzle 文档](http://docs.guzzlephp.org/en/latest/#)

命名空间中引入 guzzle 依赖

```
use GuzzleHttp\Client;
use GuzzleHttp\Exception\GuzzleException;
```

保存头像，这里使用 md5 加密 url 作为文件名，基本可以保证唯一性。后缀选择 jpg，不知道会不会引起问题，暂时先这么处理了。

```
if (!empty($avatar)) {
    if (strpos($avatar, 'http://') === 0 || strpos($avatar, 'https://') === 0) {
        $client = new Client(['verify' => false]);  //忽略SSL错误
        $path = APP_ROOT . config('common.picture_path.user_avatar_path') . md5($avatar) . '.jpg';
        $response = $client->get($avatar, ['save_to' => $path]);  //保存远程url到文件
        if ($response->getStatusCode() == 200) {
            $avatar = md5($avatar) . '.jpg';
        }
    }
}
```

将生成后的文件名入库即可。




