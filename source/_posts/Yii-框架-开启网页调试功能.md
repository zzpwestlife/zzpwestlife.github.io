---
title: Yii-框架-开启网页调试功能
date: 2016-12-30
tags: Yii
---

![](http://upload-images.jianshu.io/upload_images/693141-6753b35ab3060072.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

## 1. 首先在入口文件 `index.php`  开启 debug 模式

```
defined('YII_DEBUG') or define('YII_DEBUG',true);
```

## 2. `protected/config/main.php` 的 log 下面
```
'log' => array(
            'class' => 'CLogRouter',
            'routes' => array(
                array(
                    'enabled' => true,
                    'class' => 'CWebLogRoute',
                    'levels' => '',
                    'categories' => '',
                ),
...
```

就这么简单。


