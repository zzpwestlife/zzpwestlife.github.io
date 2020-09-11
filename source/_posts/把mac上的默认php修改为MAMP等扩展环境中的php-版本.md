---
title: 把 mac 上的默认 php 修改为 MAMP 等扩展环境中的 php 版本
date: 2017-01-03
tags: php
---

1. 把mac上的默认php修改为MAMP等扩展环境中的php 版本，也就是不使用 mac 自带的。步骤如下：

<!--more-->

打开terminal。输入```locate .bash_profile```， 找到我们需要编辑的文件的路径。
cd 到该目录下执行```sudo vim .bash_profile```,这个文件也有可能没有，如果没有就新建一个。
在vim中打开或新建好该文件后在文件末尾添加
```export PATH="/Applications/MAMP/bin/php/php5.6.10/bin:$PATH"```
注：“=”号之后，“：”号之前的路径就是你要修改成的php的路径，我这里选取的是MAMP中自带的5.6版本。
保存后运行： ```source .bash_profile```，可能会报错，不用管。
就大功告成了，接着我们验证一下，另开terminal，输入which PHP,有没有发现默认php的路径已经修改好了呢。接下来就可以正常使用了，如果还不行，重启电脑就可以了。

坑：在 /etc/profiles 中设置了环境变量后, 还是不能在 zsh 中使用. 是因为没有在 .zshrc 中配置.

在终端中输入: `cat ~/.zshrc` 以此来查看 .zshrc 文件, 找到里面的 ` # User configuration ` 部分. 可以看到当前 zsh 支持的所有本地已配置环境变量.
在 `export PATH=”XXXX”` 里面追加一条想要配置的环境变量路径.


