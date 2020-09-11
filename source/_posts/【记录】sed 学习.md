---
title: 【记录】sed 学习
date: 2017-05-25 23:11:12
tags: sed
categories: Linux
---

本文是慕课网 [《实例妙解sed 和 awk 的秘密》》](http://www.imooc.com/learn/819) 的学习记录的第二部分：sed。

Linux 三大利器： grep, sed, awk.

<!--more-->

## 1. sed 工作原理

sed 是一种流处理编辑器

1. 文本或管道输入（正则选定文本）
2. 读入一行到模式空间（临时缓冲区）
3. sed命令处理
4. 结果输出到屏幕
5. 重复第2步操作

sed 一次处理一行内容

sed 不改变文件内容 （除非重定向）


### 2. 使用 sed 格式

- 命令行格式

```
$ sed [options] 'command' file(s)
```

options: -e, -n

command: 行定位（正则） + sed命令 （操作）

- 脚本格式

```
$ sed -f scriptfile file(s)
```

举个栗子：

```
// p 即 print，打印命令
$ sed -n 'root/p'
```

## 2. sed 基本操作命令

### 1. -p 打印相关命令

```
$ sed 'p' passwd
```

结果每一行都被输出了两次，为什么？

sed 原理为读入一行，输出一行到屏幕，现在中间又加入一个打印的步骤，所以就出现了重复。

如何规避？

再添加一个 -n 参数即可。该参数一般与 p 参数一起使用，表示只打印出相关的行。

```
$ sed -n 'p' passwd
```

### 2. 行定位

- 定位一行： x; /pattern/;

```
// 打印第33行
$ sed -n '33p' passwd
_svn:*:73:73:SVN Server:/var/empty:/usr/bin/false
```

通过 nl 命令查看行号

```
// 打印第33行
$ nl passwd | sed -n '33p'
    33	_svn:*:73:73:SVN Server:/var/empty:/usr/bin/false
```

```
// 打印包含root的行
$ sed -n '/root/p' passwd
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_cvmsroot:*:212:212:CVMS Root:/var/empty:/usr/bin/false
```

- 定位多行（范围）： x,y; /pattery/,x;

```
// 打印10到20行
$ nl passwd | sed -n '10,20p'
```

```
// 打印第一个包含root的行到第一个包含www的行之间所有的行
$ nl passwd | sed -n '/root/,/www/p
```

都是匹配第一个

- 取反操作 !


```
// 不打印第20行
$ nl passwd | sed -n '20!p'
```

```
// 不打印10到20行
$ nl passwd | sed -n '10,20!p'
```

- 定位间隔几行 first~step

```
// 输出奇数行，Mac亲测无效，CentOS 有效
$ nl passwd | sed -n '1~2p'
     1	root:x:0:0:root:/root:/bin/bash
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     ...
```

### 3. sed 行处理命令

#### 1. 基本操作命令（Mac下失效）

a：新增行

i：插入行

c：替代行

d：删除行

```
// 在第5行后面添加分隔线
$ nl passwd | sed '5a ==============='
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
===============
     6	sync:x:5:0:sync:/sbin:/bin/sync
     ...
```

```
// 在第1-5行后面添加分隔线
$ nl passwd | sed '1,5a ==============='
     1	root:x:0:0:root:/root:/bin/bash
===============
     2	bin:x:1:1:bin:/bin:/sbin/nologin
===============
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
===============
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
===============
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
===============
     6	sync:x:5:0:sync:/sbin:/bin/sync
     ...
```

```
// 在第1-5行之前添加分隔线
$ nl passwd | sed '5i ==============='
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
===============
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

```
// 第5行替换为其他内容
$ nl passwd | sed '5c ==============='
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
===============
     6	sync:x:5:0:sync:/sbin:/bin/sync
```

```
$ nl passwd | sed '1,5c ==============='
===============
     6	sync:x:5:0:sync:/sbin:/bin/sync
     7	shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```

> 注意替换规则与插入规则的区别，连续行的替换是整体替换，而不是一行一行。

随意感受一下

```
$ nl passwd | sed '/nologin/c ==============='
     1	root:x:0:0:root:/root:/bin/bash
===============
===============
===============
===============
     6	sync:x:5:0:sync:/sbin:/bin/sync
     7	shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
     8	halt:x:7:0:halt:/sbin:/sbin/halt
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
===============
    25	zzp:x:1000:1000::/home/zzp:/bin/bash
===============
```

```
// 删除行，不会影响源文件
$ nl passwd | sed '/root/d'
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     6	sync:x:5:0:sync:/sbin:/bin/sync

```
#### 2. 案例1 修改配置文件

简单的操作就可以替代 vim 了

```
// 在文档末尾增加一些内容
$ sed '$a new line \nnew line'
```

#### 3. 案例2 删除文本中的空行

```
$ sed '/^$/d' file
```

#### 4. 案例3 log 中提取 error

```
$ sed -n '/Error/p' file
```

### 4. sed 替换命令 s （核心，重点）

```
$ sed 's/nologin/login/' passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/login
daemon:x:2:2:daemon:/sbin:/sbin/login
adm:x:3:4:adm:/var/adm:/sbin/login
lp:x:4:7:lp:/var/spool/lpd:/sbin/login
```
```
$ sed 's/:/%/' passwd
root%x:0:0:root:/root:/bin/bash
bin%x:1:1:bin:/bin:/sbin/nologin
daemon%x:2:2:daemon:/sbin:/sbin/nologin
adm%x:3:4:adm:/var/adm:/sbin/nologin
lp%x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

这里出现了一个问题，每一行只替换了第一个匹配。

添加全局标记 g 即可

```
$ sed 's/:/%/g' passwd
root%x%0%0%root%/root%/bin/bash
bin%x%1%1%bin%/bin%/sbin/nologin
daemon%x%2%2%daemon%/sbin%/sbin/nologin
adm%x%3%4%adm%/var/adm%/sbin/nologin
lp%x%4%7%lp%/var/spool/lpd%/sbin/nologin
```

#### 1. 案例：获取网卡中的ip

思路：首先取出 ifconfig 命令输出的第二行，然后将 ip 前后两部分无用的信息分别替换为空即可。

```
// 取出所需行
$ ifconfig | sed -n '/inet .*broadcast.*/p'
        inet 172.17.147.89  netmask 255.255.240.0  broadcast 172.17.159.255
```

```
// 替换ip前的字符为空
$ ifconfig | sed -n '/inet .*broadcast.*/p' | sed 's/inet //'
        172.17.147.89  netmask 255.255.240.0  broadcast 172.17.159.255
```

```
// 替换ip后的字符为空
$ ifconfig | sed -n '/inet .*broadcast.*/p' | sed 's/inet //' | sed 's/netmask.*//'
        172.17.147.89
```

```
// 替换可能漏掉的空格
$ ifconfig | sed -n '/inet .*broadcast.*/p' | sed 's/inet //' | sed 's/netmask.*//' | sed 's/\ //'
       172.17.147.89
```

## 3. sed 高级操作命令

### 1. -{ }： 多个sed命令，用；分开

```
// 先将2-10行删除，然后将false替换为true，两个命令写到一起，这里{}不用转义
$ nl passwd | sed '{2,10d;s/false/true/}'
     1	root:x:0:0:root:/root:/bin/bash
    11	games:x:12:100:games:/usr/games:/sbin/nologin
    12	ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
    13	nobody:x:99:99:Nobody:/:/sbin/nologin
```

### 2. -n 读取下一个输入行（用下一个命令处理）

有一个跳行的过程

```
$ nl passwd | sed -n '{n;p}'
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
     6	sync:x:5:0:sync:/sbin:/bin/sync
     8	halt:x:7:0:halt:/sbin:/sbin/halt
```

这就实现了偶数行输出。如何输出奇数行？先输入p后跳行即可。

```
$ nl passwd | sed -n '{p;n}'
     1	root:x:0:0:root:/root:/bin/bash
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     7	shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```

之前的等效做法

```
$ nl passwd | sed -n '1~2p'
$ nl passwd | sed -n '2~2p'
```

2,5,8... 这样输出呢？

```
// 两种方法等效，推荐使用前者
$ nl passwd | sed -n '2~3p'
$ nl passwd | sed -n '{n;p;n}'
```

### 3. & 符号使用

-&: 替换固定字符串
优化替换操作。

案例： passwd 文件中的用户名后面添加空格，便于阅读。

分析：
- 用户名都是在行首，可以包含字母，中划线和下划线。

使用 &，就没有必要在替换后的字符串中将替换前的正则重新再写一遍。

```
$ sed 's/^[a-z_-]\+/&   /' passwd
root   :x:0:0:root:/root:/bin/bash
bin   :x:1:1:bin:/bin:/sbin/nologin
daemon   :x:2:2:daemon:/sbin:/sbin/nologin
```

原来的写法可能是这样的，还不一定能实现。

```
$ sed 's/^[a-z_-]\+/^[a-z_-]\+  /' passwd
```

#### 案例： 大小写转换

将用户名的首字母转化为大写/小写

元字符 
- \u \l (首字母转换) 
- \U \L (字符串转换) 
- uppercase, lowercase

```
$ sed 's/^[a-z_-]\+/\u&/' passwd
Root:x:0:0:root:/root:/bin/bash
Bin:x:1:1:bin:/bin:/sbin/nologin
Daemon:x:2:2:daemon:/sbin:/sbin/nologin
Adm:x:3:4:adm:/var/adm:/sbin/nologin
```

```
$ sed 's/^[a-z_-]\+/\U&/' passwd
ROOT:x:0:0:root:/root:/bin/bash
BIN:x:1:1:bin:/bin:/sbin/nologin
DAEMON:x:2:2:daemon:/sbin:/sbin/nologin
```

文件名转大写

```
$ ls *.txt | sed 's/^\w\+/\U&/'
```


### 4. （）符号

一个括号代表一个要被替换的部分。注意转义括号。

示例：数据筛选，获取passwd中 USER, UID  和 GID

一个一个获取

```
$ sed 's/\(^[a-z_-]\+\):.*$/\1/' passwd
root
bin
daemon
adm
```

```
$ sed 's/\(^[a-z_-]\+\):x:\([0-9]\+\).*$/\1 \2/' passwd
root 0
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
```

```
$ $ sed 's/\(^[a-z_-]\+\):x:\([0-9]\+\):\([0-9]\+\).*$/USER:\1 UID:\2 GID:\3/' passwd
USER:root UID:0 GID:0
USER:bin UID:1 GID:1
USER:daemon UID:2 GID:2
USER:adm UID:3 GID:4
USER:lp UID:4 GID:7
USER:sync UID:5 GID:0
USER:shutdown UID:6 GID:0
USER:halt UID:7 GID:0
USER:mail UID:8 GID:12
USER:operator UID:11 GID:0
USER:games UID:12 GID:100
USER:ftp UID:14 GID:50
USER:nobody UID:99 GID:99
```

示例： 获取网卡IP

分析：之前我们是将IP行分为三段，分别将前后两段无用的信息替换为空。现在我们还是将该行分为三段，但是只要获取第二段即可。

先找到IP所在的行，匹配第一部分，注意别漏了空格。第二部分用括号括起来，用于输出，第三部分不用管。最后将第二部分打印输出即可。

```
$ ifconfig | sed -n '/inet /p' | sed -n '1p' | sed 's/[a-z ]\+\([0-9.]\+\) .*$/\1/'
172.17.147.89
```

### 5. rw 命令

读和写命令，文件互操作

r: 复制指定文件插入到匹配行

w: 复制匹配行拷贝至指定文件

```
$ echo -e '11111\n22222\n33333' > 123.txt
$ echo -e 'aaaa\nbbbbb\ncccc' > abc.txt
```

```
// 读取文件123.txt，将结果放入到abc.txt 中，放置位置为第一行后。
$ sed '1r 123.txt' abc.txt
aaaa
11111
22222
33333
bbbbb
cccc
```

如果不指定行号，将会在目录文件的每一行后面插入读取到的文件内容。

读文件后，两个文件的内容都没有发生改变。

```
// 将123.txt文件的第一行写入abc.txt
$ sed '1w abc.txt' 123.txt
```

写操作后，abc.txt内容受影响，原内容消失，只剩下本次操作写入的内容。不指定行号就相当于文件内容拷贝。

引号中是rw直接操作的文件。使用w的时候要格外小心。

### 6. q 命令

退出 sed

在执行中间提前退出sed，没必要每次都执行到最后一行。

```
// 执行到第5行退出sed
$ nl passwd | sed '5q'
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
// 找到第一个 nologin 就退出 sed
$ nl passwd | sed '/nologin/q'
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
```

![](http://omp48p40q.bkt.clouddn.com/17-5-25/45710249.jpg)


