---
title: hexo 搭建个人博客的学习记录-yelee 主题设置
date: 2017-04-24
tags: hexo
description: 对比了很多主题，最后采用 Yelee. 无他，看着顺眼罢了。
---

对比了很多主题，最后采用 [Yelee](https://github.com/MOxFIVE/hexo-theme-yelee)

[Yelee 文档](http://moxfive.coding.me/yelee/)

<!--more-->

## 1. 安装

首先，确保已安装 [hexo](https://github.com/hexojs/hexo)

```
git clone https://github.com/MOxFIVE/hexo-theme-yelee.git themes/yelee
```

再修改 Hexo 根目录对应配置文件 `_config.yml`，即可切换到 Yelee 主题

```
theme: yelee
```

执行以下命令预览当前主题

```
hexo clean && hexo g
```

### 语言切换

```
language: zh-Hans
```

### Https

如果站点通过 HTTPS 访问，那下列的服务可能 无法正常使用：

- 多说评论
- 友言评论
- 百度分享
- 百度统计

可使用下列支持 HTTPS 的服务替代：

- Disqus 评论
- AddThis
- 谷歌分析


## 2. 基本设置

### 头像

默认头像存储于 `yelee/source/img/avatar.png`

配置中对应填写 `/img/avatar.png`，可替换图片或指定新地址

我上传了自己的头像，尺寸为 150*150

```
avatar: /img/zzp.png
```

### 文章摘要

两种在文章列表显示摘要的方式

1. 正文中添加 `<!-- more -->`
2. 文件头添加 description 标签，内容就是显示在文章列表中 摘要，如：

```
description: "这里写要显示在文章列表的摘要。"
```

### 评论系统

等备案完成后再来补充

### 标签云页面

使用 Hexo 命令新建一个名为 tags 的页面即可

```
hexo new page tags
```

该页面标题可以在文件 `\hexo\source\tags\index.md` 中修改

### 关于我页面

使用 Hexo 命令新建一个名为 about 的页面即可

```
hexo new page about
```

该页面内容在文件 `\hexo\source\about\index.md` 中修改

### 站点小图标

若将图标存储于 `yelee/source/favicon.png`

则配置中对应填写 `/favicon.png`，另外填网络图片的地址也可

`favicon: /favicon.png`

[在线制作小图片](http://tool.lu/favicon/)




