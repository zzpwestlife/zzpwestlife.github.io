---
title: hexo 搭建个人博客的学习记录-Git 自动部署代码到服务器
date: 2017-04-18
tags: git
description: 这个世界是由懒人驱动的。PS, 参考这类技术文章还是要找原文，国内的翻译，不说了。
---

<style>
  .img {
    width: 60%;
    text-align: center;
  }
</style>

<div class='img'>
![](http://omp48p40q.bkt.clouddn.com/17-5-7/51656771-file_1494155724132_a08a.png)
</div>


> 这个世界是由懒人驱动的。 PS: 参考这类技术文章还是要找原文，国内的翻译，不说了。

<!-- more -->


之前常用的部署代码就是用svn，或是更老土的ftp。每次的git pull实在是一个让人烦躁的东西，就网上查找了一下，发现git-hook 是个好东西。[参考原文](http://toroid.org/git-website-howto)


实现原理是当我们push 代码到`remote repository`时，通过git的`post-receive hooks`。执行
`git checkout prod -f`
来帮助我们实现自动部署。

## 1. 在**本地**创建一个`git repository`

```
$ mkdir test && cd test
$ git init 
$ echo 'Hello, world!' > index.html
$ git add index.html
$ git commit -q -m "init"
```

`index.html` 就是我们希望能够部署到服务器的代码

## 2. 然后在**服务**器创建一个`repository`

要注意，服务器要设置两个目录，一个是 git 目录，一个是代码目录。服务器的git目录和本地的代码目的都要指向服务器的代码目录。

这里可不是服务器部署代码的位置

```
$ mkdir test.git && cd test.git
$ git init --bare
$ cat > hooks/post-receive
#!/bin/sh
GIT_WORK_TREE=/dir/to/your/webpage/root git checkout -f

#!/bin/sh
GIT_WORK_TREE=/root/www/test git checkout  -f  // 这个是我的网站路径，这就是服务器git目录指向服务器代码目录的方式
$ chmod +x hooks/post-receive
```

这是服务器的git代码目录

```
/dir/to/test.git
```

这里的 `/root/www/test` 就是我将要部署服务器代码的位置，一般的lamp，我们喜欢放在www里，当然这里需要根据不同的环境更换就好了。

## 3. 在本地的git目录下增加一个remote

```
$ git remote add web ssh://root@47.93.234.60:/root/www/test.git
$ git push web +master:refs/heads/master
```

server_address 可以是 ip，域名。

也可以使用 SourceTree 来实现上面两个步骤。

这时我们切换到服务器目录下，就可以看到我们的`index.html` 在我们向`web push`的之后，已经自动`check out` 到我们指定目录下了。

之后我们只需要修改完成之后，`git push web` 就可以自动部署代码了。

SourceTree

1. New Repository -> add Existing Local Repository -> 选中本地代码目录
2. 更改文件后添加并push即可。 


我的做法是把 hexo 的 public 目录作为本地代码目录，编译生成静态文件后通过git上传到服务器代码目录，然后nginx将站点指向服务器代码目录即可。服务器上不需要配置hexo，只保留静态文件即可。

在本地写markdown文件并调试好再上传。

最后还是决定同步整个 hexo 文件夹。本地和服务器都可以做更改。但在服务器上做的更改需要注意，可能会被本地的更改冲掉。所以尽量在本地做更改。

