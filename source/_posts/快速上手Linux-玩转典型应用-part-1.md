---
title: 快速上手Linux 玩转典型应用 part-1
date: 2017-11-13 23:54:17
tags: Linux
categories: Liunx
---

慕课网 [快速上手Linux 玩转典型应用](http://coding.imooc.com/learn/list/154.html) 学习笔记  part-1

[在线学习点这里](http://www.runoob.com/linux/linux-tutorial.html)

<!-- more -->

## 1. 研发必须知道的 Linux 知识

- Linux 区分大小写
- 一切皆文件
- 文件名后缀只是为了便于识别，对系统没有任何意义
- 云服务器与本地虚拟机的最主要区别就是云服务器有**公网IP**

## 2. 准备知识

### 1. 查看 ip

#### ifconfig （最常用）
```
[zzp@iZ2ze4kikm9076bxac1noqZ ~]$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.147.89  netmask 255.255.240.0  broadcast 172.17.159.255
        ether 00:16:3e:2e:0c:1e  txqueuelen 1000  (Ethernet)
        RX packets 14729288  bytes 2106329863 (1.9 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 9911393  bytes 8389800747 (7.8 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1  (Local Loopback)
        RX packets 10772426  bytes 667897558 (636.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10772426  bytes 667897558 (636.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

#### ip addr
```
[zzp@iZ2ze4kikm9076bxac1noqZ ~]$ ip addr
# 本地回环ip
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
# 物理ip
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:16:3e:2e:0c:1e brd ff:ff:ff:ff:ff:ff
    inet 172.17.147.89/20 brd 172.17.159.255 scope global eth0
       valid_lft forever preferred_lft forever
```

#### vi ifcfg-xx
```
[zzp@iZ2ze4kikm9076bxac1noqZ ~]$ vi /etc/sysconfig/network-scripts/ifcfg-xx
# 这里 xx 就是 ip addr 获取到里的两个ip的名字， lo和eth0
```

修改 `ONBOOT=yes`， 并`service network restart`，对应的网络信息就可以在 `ip addr` 下显示。

#### yum install net-tools
安装完成后就可以使用ifconfig命令（minial 版的centOS阉割了此命令）

### 2. 替换默认源（软件下载地址）

操作方法见此[地址](http://mirrors.163.com/.help/centos.html)

### 3. 安装 vim
```
yum install vim
```

## 3. 远程服务管理工具ssh （重点）

Secure Shell 安全外壳协议，建立在应用层基础上的安全协议。

可靠，专为远程登录会话和其他网络服务提供安全性的协议。

### 1. 安装 ssh 服务

服务器端一般默认已安装了ssh服务

```
yum install openssh-server
```
### 2. 启动 ssh
```
service sshd start
ps -ef | grep ssh
```
### 3. 设置开机启动
```
chkconfig sshd on
```

客户端安装 ssh 工具

Win:Xshell, Putty, secureCRT 等，
Linux: `yum install openssh-clients`

连接：
`ssh zzp@47.93.234.60 -p 8222`

### 4. ssh config 命令
- 为了方便批量管理多个ssh
- 存放在 ~/.ssh/config

config 语法：
1. Host: 别名
2. HostName: 主机名或ip
3. Port: 端口 默认 22
4. User: 用户名
5. IdentityFile: 密钥文件路径

```
[root@iz2ze4kikm9076bxac1noqz ~]# cd .ssh/
[root@iz2ze4kikm9076bxac1noqz .ssh]# ll
total 4
-rw------- 1 root root 403 May 24 15:20 authorized_keys
[root@iz2ze4kikm9076bxac1noqz .ssh]# touch config
```
### 5. 免密登录 ssh key
使用非对称加密方式生成公钥和私钥，私钥存放在本地 ~/.ssh，保护好私钥，公钥可以对外公开，放在服务器的 ~/.ssh/authorized_keys

```
ssh-keygen -t rsa
ssh-keygen -t dsa
```

需要加载私钥
```
ssh-add ~/.ssh/private_key
```

### 5. [iTerm 中配置免密登录 sshpass](http://www.cnblogs.com/onlyfu/p/4460160.html)

我的配置大概下面这个样子
```
/usr/local/bin/sshpass  -f /zzp/sshpass/my_aliyun_pass ssh zzp@47.93.234.60 -p 8222
```

其中，`/zzp/sshpass/my_aliyun_pass` 文件中存储的是登录密码，设置好快捷键后就可以实现一键免密登录。

### 6. 端口安全
改变默认服务器端口，修改 `/etc/ssh/sshd_config` 文件，大概十几行， Port 12345，并且可以同时监听多个端口

```
Port 12345
Port 10086
Port 10010
```

## 4. Liunx 常用命令

### 1. 软件操作命令

软件包管理器 yum
- 安装软件 `yum install xxx`
- 卸载软件 `yum remove xxx`
- 搜索软件 `yum search xxx`
- 清理缓存 `yum clean packages`
- 列出已安装 `yum list` 会列出很多，最好grep一下
- 软件包信息 `yum info xxx`

### 2. 服务器硬件资源信息

- 内存 `free -m`
- 硬盘 `df -h`
- 负载 `w/top`
- cpu 个数和核数 `cat /proc/cpuinfo`

1分钟，5分钟，15分钟的平均负载，一般大于0.6或0.7为危险

```
$ w
21:13:30 up 1 day, 20:22,  1 user,  load average: 0.00, 0.03, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
zzp      pts/0    123.122.85.229   21:05    2.00s  0.00s  0.00s w
```

fdisk 可视化磁盘

### 3. 文件操作命令

文件目录结构
- 根目录 /
- 家目录 /home
- 临时目录 /tmp
- 配置目录 /etc
- 用户程序目录 /usr

文件操作
- 查看目录下的文件 ls/ll/ll -ah
- 新建文件 touch
- 新建文件夹 mkdir (mkdir -p dir1/dir2/dir3/dir4)
- 进入目录 cd
- 删除文件和目录 rm
- 复制 cp
- 移动 mv
- 显示当前路径 pwd (print working directory)

### 4. Vim

- 首行 gg
- 末行 G
- 行尾 $
- 删除 dd
- 恢复 u
- 复制 yy
- 粘贴 p 

### 5. 文件搜索、查找、读取
- 从文件尾部开始读 tail 通常加 -f
- 从文件头部开始读 head
- 读取整个文件 cat 注意大文件，不要随意读
- 分页读取 more 从头部开始读
- 可控分页 less 
- 搜索关键字 grep  (grep -n "error" filename)
- 查找文件 find
- 统计个数 wc (cat filename | wc -l)  wordcount

`grep "2017-11-11" filename | more`

`find . -name "*.conf"` 在当前目录及其子目录下查找名称以.conf结尾的文件

`find . -type f` 在当前目录及其子目录下查找类型为文件的文件

`find . -type d` 在当前目录及其子目录下查找类型为目录的文件

`find . -ctime -20` 在当前目录及其子目录下查找所有最近20天内更新过的文件

`find /var/log -type f -mtime +7 -exec rm {}; \` 在/var/log下查找所有7天以前更新过的文件，删除

`find . -type f -perm 644 -exec ls -al {} \;`

### 6. 文件解压缩

`tar -cf des.tar source` create compressed file from source to des

`tar -tvf file.tar` list files in the compressed file

`tar -xf file.tar` extract file from the compressed file

`tar -czvf des.tar.gz source`  in .gz format, visualized, -f usually is the last param

`tar -xzvf file.tar.gz`

记住每个参数的意思

### 7. 系统用户操作命令

- useradd
- adduser
- userdel
- passwd


> useradd is native binary compiled with the system. But, adduser is a perl script which uses useradd binary in back-end.
adduser is more user friendly and interactive than its back-end useradd. There's no difference in features provided.

### 8. 防火墙的设置

保护服务器安全

确认服务是否已安装
```
# yum list | grep firewall
firewalld.noarch                        0.4.4.4-6.el7                  @base
firewalld-filesystem.noarch             0.4.4.4-6.el7                  @base
python-firewall.noarch                  0.4.4.4-6.el7                  @base
fail2ban-firewalld.noarch               0.9.7-1.el7                    epel
firewall-applet.noarch                  0.4.4.4-6.el7                  base
firewall-config.noarch                  0.4.4.4-6.el7                  base
puppet-firewalld.noarch                 0.1.3-1.el7                    epel
system-config-firewall.noarch           1.2.29-10.el7                  base
system-config-firewall-base.noarch      1.2.29-10.el7                  base
system-config-firewall-tui.noarch       1.2.29-10.el7                  base
```

确认服务是否已启动
```
# ps -ef | grep firewall
root     19678 19531  0 22:45 pts/1    00:00:00 grep --color=auto firewall
```

安装
```
# yum install firewalld
```

启动
```
$ service firewalld start
Redirecting to /bin/systemctl start  firewalld.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: root
Password:
==== AUTHENTICATION COMPLETE ===
```

检查状态
```
# service firewalld status
Redirecting to /bin/systemctl status  firewalld.service
● firewalld.service
   Loaded: masked (/dev/null; bad)
   Active: inactive (dead)
```

关闭或禁用
```
# service firewalld stop/disable
```

```
firewall-cmd --version
firewall-cmd --state
firewall-cmd --get-zones
firewall-cmd --get-default-zone
firewall-cmd --list-all-zone
firewall-cmd --zone=public
firewall-cmd --list-ports
firewall-cmd --query-service=ssh
firewall-cmd --remove-service=ssh
firewall-cmd --add-service=ssh
firewall-cmd --list-services
firewall-cmd --query-port=22/tcp
firewall-cmd --add-port=22/tcp
firewall-cmd --remove-port=22/tcp
firewall-cmd --list-ports
firewall-cmd --get-all-zones
```

### 9. 提权和文件上传下载

- 提权 sudo
- 下载 wget, curl
- 上传 scp


secureCRT + lrzsz

#### 1. 提权
```
visudo
```
找到 Allows people in group wheel to run all commands

添加一行
```
%user ALL=(ALL)  ALL
```
将用户添加到提权账号中

尽量不要使用root 账号进行操作。

#### 2. 下载

一般使用 wget 就可以了

```
# wget http://www.baidu.com/
--2017-11-13 23:33:40--  http://www.baidu.com/
Resolving www.baidu.com (www.baidu.com)... 115.239.210.27, 115.239.211.112
Connecting to www.baidu.com (www.baidu.com)|115.239.210.27|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2381 (2.3K) [text/html]
Saving to: ‘index.html’

100%[====================================================================================================================>] 2,381       --.-K/s   in 0s

2017-11-13 23:33:40 (260 MB/s) - ‘index.html’ saved [2381/2381]

[root@iz2ze4kikm9076bxac1noqz ~]# ll
-rw-r--r--  1 root root     2381 Jan 23  2017 index.html

# curl -o baidu.html http://www.baidu.com
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2381  100  2381    0     0   3918      0 --:--:-- --:--:-- --:--:--  3922
[root@iz2ze4kikm9076bxac1noqz ~]# ll
-rw-r--r--  1 root root     2381 Nov 13 23:34 baidu.html
-rw-r--r--  1 root root     2381 Jan 23  2017 index.html
```

#### 3. 上传
```
scp file root@192.168.1.1:/dir/
```

下载到本地
```
scp root@192.168.1.1:/dir/file locate_dir
```













