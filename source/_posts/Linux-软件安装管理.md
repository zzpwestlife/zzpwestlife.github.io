---
title: Linux 软件安装管理
date: 2018-02-18 12:19:34
tags: Linux
---

## linux 安装软件

只有两种方式，源码（二进制文件）编译和 rpm 包，yum 其实也是 rpm 包的一种在线安装方式，并解决了依赖问题。但是不支持查询和校验。

<!-- more -->

### 1. rpm 安装

#### 1. 安装
```
rpm -ivh 软件包全名
rpm -ivh httpd-2.2.15-15.e16.centos.1.i686.rpm
```

- i：install
- v：verbose，显示指令执行过程；
- h：hash，显示安装百分比

#### 2. 升级
```
rpm -Uvh 软件包全名
rpm -Uvh httpd-2.2.15-15.e16.centos.1.i686.rpm
```

- U：upgrade 升级

如果没有安装过，相当于全新安装

#### 3. 卸载

```
rpm -e 包名，而不是包全名，只有安装和升级需要包全名
rpm -e httpd // 系统会去 /var/lib/rpm 目录下的数据库中找
```

- e：erase，删除
- --nodeps：不检查依赖，实际环境中不允许使用

卸载的依赖顺序和安装时的依赖顺序相反

rpm 包一定要解决依赖性才可以正确安装和卸载

为什么会有卸载命令？源码安装就没有卸载命令。因为 rpm 包安装的位置为系统默认位置，分布在系统的各个位置，想要卸载所有的文件非常麻烦，所以设置了一个卸载命令。


#### 4. rpm 包查询

##### 1. 系统是否已安装软件
```
rpm -q 包名
rpm -q httpd
```
- q：query
- a：all

##### 2. 查询系统中所有已安装的软件
```
rpm -qa | grep httpd
```

##### 3. 查询软件包详细信息
```
rpm -qi 包名
rpm -qi httpd
```

- i：information
- p：package，查询未安装包信息

```
rpm -qip 包全名
rpm -qi httpd-2.2.15-15.e16.centos.1.i686.rpm
```

##### 4. 查询包中文件安装位置
```
rpm -ql 包名
```

- l：list，列表

##### 5. rpm 包默认安装路径

路径 | 说明
- | :-: 
/etc | 配置文件安装目录
/usr/bin/ | 可执行命令安装目录
/usr/lib | 程序所使用的函数库保存位置
/usr/share/doc/ | 基本的软件使用手册保存位置
/usr/share/man/ | 帮助文件保存位置


##### 6. 查询系统文件（rpm 安装生成的文件）属于哪个 rpm 包
```
rpm -qf 系统文件名
```

##### 7. 查询软件包的依赖性
```
rpm -qR 包名
rpm -qR httpd
```
查询结果没什么意义，还不如一步步安装，根据提示来安装各种依赖

- R：requires

#### 4. rpm 包校验

包含 md5 校验、文件修改时间校验等

```
rpm -V 已安装的包名
rpm -V httpd
```

- V：verify

如果没有任何输出，表示校验通过，文件没有被修改


#### 5. rpm 包内文件提取

```
rpm2cpio 包全名 | cpio -idv .文件绝对路径
```

- rpm2cpio：将 rpm 包转换为 cpio 格式的命令
- cpio：是一个标准工具，它用于创建软件档案文件和从档案文件中提取文件

```
cpio 选项 < [文件|设备]
```

- i：copy-in 模式，还原
- d：还原时自动新建目录
- v：显示还原过程

示例
##### 1. 查询 ls 命令属于哪个 rpm 包

```
rpm -qf /bin/ls
``` 

##### 2. 造成 ls 命令误删除假象

```
mv /bin/ls /tmp/
```

##### 3. 提取 rpm 包中 ls 命令到当前目录的 /bin/ls
 下

```
rpm2cpio /mnt/cdrom/Packages/coreutils-8.4-19.el6.i686.rpm | cpio -idv ./bin/ls
```

##### 4. 把 ls 命令复制到 /bin/ 目录，修复文件丢失

```
cp /root/bin/ls /bin/
```

### 2. yum 在线安装

网络下载

rpm 包的安装过程中，依赖性太强，使用难度较大。

yum 好处：将所有软件包放到官方服务器上，当进行 yum 在线安装时，可以自动解决依赖性问题。

Redhat 的 yum 在线安装需要付费，CentOS 的 yum 免费

#### 1. yum 源文件

/etc/yum/repos.d/CentOS-Base.repo

```
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
```

![](http://omp48p40q.bkt.clouddn.com/18-2-18/43605225.jpg)


#### 2. yum 命令

##### 1. 查询

查询所有可用软件包列表
```
yum list
```

搜索服务器上所有和关键字相关的包
```
yum search 关键字
```


##### 2. 安装

yum 安装不需要写包全名

```
yum -y install 包名
yum -y install gcc
```

- install：安装
- y：自动回答 yes

##### 3. 升级

```
yum -y update 包名
```

<b style='color:red'>稳定和安全为主，没必要尽量不要升级</b>

##### 4. 卸载

```
yum -y remove 包名
```

卸载也是要卸载各种依赖，很容易造成系统崩溃

<b style='color:red'>服务器使用最小化安装，用什么软件安装什么，尽量不卸载</b>

#### yum 软件组管理命令

列出所有可用的软件组列表
```
yum grouplist
```

安装指定软件组，组名可以由 grouplist 查询处理
```
yum groupinstall 软件组名（必须英文）
yum groupinstall "Chinese Support"
```

卸载指定软件组
```
yum groupremove 软件组名
```
### 3. 源码包和 rpm 包的区别

- 安装之前的区别：概念上的区别
- 安装之后的区别：安装位置不同


rpm 包安装在默认位置（包作者所指定的位置），可以指定安装位置，但是强烈不建议这么做。

#### 1. 安装位置不同带来的影响

rpm 包安装的服务可以使用系统服务管理命令（service）管理，例如 rpm 包安装的 Apache 的启动方法是

```
/etc/rc.d/init.d/httpd start # linux 标准启动方法
service httpd start
```
<b style="color:red">service 搜索的目录实际上就是 `/etc/rc.d/init.d/` 目录</b>

源码包安装必须指定安装位置，一般是 `/usr/local/软件名/`，相当于 Windows 下的 `C:\Programs`，因为源码包没有卸载命令，直接删除安装目录就是卸载。

源码包安装的服务不能被服务管理命令管理，因为没有安卓到默认路径中。所以只能用绝对路径进行服务的管理，如：
```
/usr/local/apache2/bin/apachectl start
```
### 4. 源码包安装软件

#### 1. 安装准备：

- 安装 C 语言编译器 `rpm -qa | grep gcc`
- 下载源码包，一般都是由官方网站提供


<b style="color:red">原则：给大量用户提供服务的软件，建议使用源码包安装，因为源码包效率更高

不太影响效率的软件，可以使用 rpm 包安装</b>

#### 2. 安装注意事项

- 源代码保存位置：`/usr/local/src`
- 软件安装位置：`/usr/local/`
- 如何确定安装过程报错
  - 安装过程停止
  - 并且最后几行出现 error、warning 或 no 的提示

#### 3. 安装过程
1. 下载源码包
2. 解压安装包
3. 进入到解压缩目录
4. `./configure` 软件配置与检查
  - 定义需要的功能选项；
  - 监测系统环境是否符合安装要求；
  - 把定义好的功能选项和检测系统环境的信息都写到 Makefile 文件，用于后续的编辑。

5.  `make` 编译，如果有报错，使用 `make clean` 清除缓存文件
6. `make install` 编译安装

### 5. 脚本安装软件

所谓的一键安装包，实际上还是安装的源码包和 rpm 包，只是把安装过程写成了脚本，便于初学者安装

优点：简单、快速、方便

缺点：
- 不能定义安装软件的版本
- 不能定义所需要的软件功能
- 源码包的优势（自定义、安装位置、依赖等）丧失





