---
title: Linux 懒人工具--autojump
date: 2017-04-10
tags: Linux
categories: Linux
description: 在终端的文件夹跳转非常麻烦, 需要敲长长的路径. alias 别名也不是很方便。autojump 是通过记录进入过的目录到数据库来实现的, 所以必须是曾经进入过的目录才能跳转.
---

![](http://omp48p40q.bkt.clouddn.com/17-4-14/72919152-file_1492142306461_3bfa.jpg)

> 在终端的文件夹跳转非常麻烦, 需要敲长长的路径.
alias 别名也不是很方便。
[autojump](https://github.com/wting/autojump) 是通过记录进入过的目录到数据库来实现的, 所以必须是曾经进入过的目录才能跳转.

<!--more-->

## 1. 安装
### 1. OS X

推荐使用 Homebrew 安装 autojump

```
brew install autojump
```

macOS 启动 Shell 自动读取的文件有

```
/etc/profile
~/.bash_profile
~/.bash_login
~/.profile
```

所以只需要在上面其中一个文件加上

```
[[ -s $(brew --prefix)/etc/profile.d/[autojump.sh](http://autojump.sh/) ]] && . $(brew --prefix)/etc/profile.d/[autojump.sh](http://autojump.sh/)
```

但如果终端工具使用的是 zsh，需要在`~/.zshrc`添加

```
[[ -s `brew --prefix`/etc/autojump.sh ]] && . `brew --prefix`/etc/autojump.sh
```

然后，运行 `source <sourcefile>`.

### 2. Linux

首先下载 autojump 源码

```
git clone git://github.com/joelthelion/autojump.git
```

然后可安装或卸载
```
cd autojump
./install.py or ./uninstall.py
```

由于Linux 下 Shell 启动会自动读取 ~/.bashrc 文件，所以将下面一行添加到该文件中

```
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

然后，运行`source ~/.bashrc`即可。

安装完成后，使用查看autojump版本。

```
$ autojump --version
autojump release-v21.1.2
```



## 2. 用法

只有打开过的目录 autojump 才会记录，所以使用时间越长，autojump 才会越智能。

可以使用 `autojump` 命令，或者使用短命令 `j`.

1. 跳转到指定目录

    ```
    j directoryName
    ```
    如果不知道目录全名，输入一部分，按Tab键就好，输错了也没关系，可以自动识别，非常强大。

    ```
    # j csm
    /data/www/xxx/cms
    ```

    Tab 键效果
  
    ```
    vagrant@homestead:~$ pwd
    /home/vagrant
    vagrant@homestead:~$ j --stat
    10.0:	/etc/nginx/conf.d
    20.0:	/home/vagrant/www/xxx/doc_api
    34.6:	/home/vagrant/www/xxx
    40.0:	/var/log/nginx
    Total key weight: 104. Number of stored dirs: 4
    vagrant@homestead:~$ j n__ (Tab 键自动添加了下划线)
    /var/log/nginx
    vagrant@homestead:/var/log/nginx$
    ```

2. 跳转到指定目录的子目录（Mac 下效果与`j`相同，Ubuntu下不好用）

    ```
    jc directoryName
    ```
    
3. 使用系统工具（Mac Finder, Windows Explorer, GNOME, etc.）打开目录，类似Mac OS terminal 下的 `open` 命令，但`open` 命令需要指定路径（Mac中还算实用，Ubuntu下不好用）

    ```
    jo directoryName
    ```

4. 查看权重 `j --stat`

    ```
    $ j --stat
    10.0:	/etc/nginx/conf.d
    10.0:	/home/vagrant/www/caijing/doc_api
    10.0:	/var/log/nginx
    30.0:	/home/vagrant/www/caijing
    Total key weight: 59. Number of stored dirs: 4
    ```

    权重越高，说明目录使用的越频繁。

    感觉 Mac 中的显示效果更好，还可以自己去调整权重值。

    ```
    $ j --stat
    10.0:	/Users/xxx/xxx/xxxx/xxxx/xxxx/vendor
    22.4:	/Users/xxx/xxx/xxxx/xxxx/xxxx/log
    
    32:	    total weight
    2:	     number of entries
    10.00:	 current directory weight
    
    data:	 /Users/xxx/Library/autojump/autojump.txt
    ```

5. 其他
    ```
    autojump --help
    ```


