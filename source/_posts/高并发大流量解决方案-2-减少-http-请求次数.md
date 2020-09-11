---
title: 高并发大流量解决方案 2 减少 http 请求次数
date: 2018-02-15 16:31:11
tags: 高并发
---

### 2. 减少 http 请求次数

#### 1. 为什么要减少 http 请求

   - 性能黄金法则
   
     只有10%-20%的最终用户响应时间花在接收请求的html文档上，剩下的80%-90%时间花在html文档所引用的所有组件（图片、script、css、flash等）进行的http请求上。
     
<!-- more -->

   - 如何改善
   
     改善响应时间最简单的途径就是减少组件的数量，并由此减少http请求的数量。    
  - http 连接产生的开销
  
    域名解析 - tcp连接 - 发送请求 - 等待 - 下载资源 - 浏览器解析


#### 2. 减少 http 请求的方式

##### 1. 图片地图
  
    图片地图允许在一张图片上关联多个url，目标url的选择取决于点击了图片上的哪个位置。
    
   如：导航的多张图片，可以使用多张分开的图片，然后每张图片对应一个超链接。这样就会产生多个图片的http请求，我们的目标是要减少http请求，将多张图片合成为一张图片，然后以位置信息定义超链接，达到减少http请求的目的。把http请求减少为一个，可以保证设计的完整性和功能的齐全性。
   
   
 示例：
 
http://stevesouders.com/hpws/imagemap-no.php

http://stevesouders.com/hpws/imagemap.php

``` html
<center>
<img usemap="#map1" border="0" src="/images/imagemap.gif?t=1518433942">
<map name="map1">
  <area shape="rect" coords="0,0,31,31" href="javascript:alert('Home')" title="Home">
  <area shape="rect" coords="36,0,66,31" href="javascript:alert('Gifts')" title="Gifts">
  <area shape="rect" coords="71,0,101,31" href="javascript:alert('Cart')" title="Cart">
  <area shape="rect" coords="106,0,136,31" href="javascript:alert('Settings')" title="Settings">
  <area shape="rect" coords="141,0,171,31" href="javascript:alert('Help')" title="Help">
</map>
</center>
```

##### 2. css Sprites

css 精灵，通过使用合并图片，通过指定css 的 `background-image` 和 `background-position` 来显示元素。

示例：

http://stevesouders.com/hpws/sprites.php

``` html
<div id="navbar" style="background-color: #F4F5EB; border: 2px ridge #333; width: 180px; height: 32px; padding: 4px 0 4px 0;">
  <a href="javascript:alert('Home')" title="Home"><span class="home"></span></a>
  <a href="javascript:alert('Gifts')" title="Gifts"><span class="gifts"></span></a>
  <a href="javascript:alert('Cart')" title="Cart"><span class="cart"></span></a>
  <a href="javascript:alert('Settings')" title="Settings"><span class="settings"></span></a>
  <a href="javascript:alert('Help')" title="Help"><span class="help"></span></a>
</div>
```

``` css
<style>
#navbar span {
  width:31px;
  height:31px;
  display:inline;
  float:left;
  background-image:url(/images/spritebg.gif?t=1518434522);
}
.home     { background-position:0 0; margin-right:4px; margin-left: 4px;}
.gifts    { background-position:-32px 0; margin-right:4px;}
.cart     { background-position:-64px 0; margin-right:4px;}
.settings { background-position:-96px 0; margin-right:4px;}
.help     { background-position:-128px 0; margin-right:0px;}
</style>
```

性能分析：图片地图和css精灵的响应时间基本相同，但比使用多个图片的方式要快50%以上。css精灵比图片地图方便一些，通常选用css精灵的方式。


##### 3. 合并脚本和样式表

使用外部css和js文件引用的方式，这比直接写在页面中性能要好一点。但这样会增加http请求数量。独立一个js文件比用多个文件载入速度快38%。

##### 4. 图片使用 base64 编码减少页面请求数

采用 base64 编码将图片直接嵌入到网页中，而不是从外部载入。但是HTML文件会变大些。

`php file_get_conetents` 读出图片文件，然后使用 `base64_encode` 函数生成。