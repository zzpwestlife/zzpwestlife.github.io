---
title: php artisan 的几个命令
date: 2017-07-15 21:00:22
tags: laravel
categories: laravel
---

数据表添加字段

```
php artisan make:migration alter_table_cms_articles_add_column_is_rewardable [--path=database/migrations/xxx] [--database=news]
php artisan migrate [--path=database/migrations/xxx] [--database=news]
```

<!--more-->

生成权限项

```
php artisan generate:perms --controller=Permission/TesterAuth
```

新建模型、控制器等

```
php artisan make:repository AdShowApp  --model=AdShowApp

php artisan make:repository Dudao/BalanceLog --model=Dudao/BalanceLog

php artisan make:controller Backend/Setting/CurrencyController
```


