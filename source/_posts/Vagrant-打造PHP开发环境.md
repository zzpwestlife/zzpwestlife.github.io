---
title: Vagrant-打造PHP开发环境
date: 2017-04-21
tags: laravel
---

![](http://upload-images.jianshu.io/upload_images/693141-dcab921466013b5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本文是慕课网 [《vagrant打造跨平台可移动的开发环境》](http://www.imooc.com/learn/805)的学习记录。

<!--more-->

ThinkPHP 和 Apache 的配置相对麻烦，且很少使用。若没有需求可直接略过这两部分。

## 1. 虚拟机
优点：
- 安装多种演示环境；
- 保证主机快速运行，减少不必要的垃圾安装程序；
- 避免每次重新安装；
- 测试不熟悉的应用，随便在虚拟机中安装和删除；
- 体验不同版本的操作系统。
****
## 2. Vagrant
是构建在虚拟化技术之上的虚拟机运行环境管理工具
#### 作用：
- 建立和删除虚拟机
- 配置虚拟机运行参数
- 管理虚拟机运行状态
- 自动化配置和安装开发环境

vagrant 的运行，需要依赖某项具体的**虚拟化技术**（virtualbox，vmvare）

#### 优点：
- 跨平台 无差异性
- 可移动 镜像和配置文件相对较小
- 自动化部署，无需人工参与
- 面试加分项

- 减少公司人力培训成本
- 统一开发环境（vagrant+virtualbox+ubuntu）

#### 适用范围
- 开发环境，不适用线上环境
- 项目配置比较复杂 （nginx/apache, memcache/redis, php/python/java）配置好的环境可以分发给每一个人，保证环境统一


#### 安装步骤
1. 下载 Virtualbox（VirtualBox-5.1.4-110228-OSX.dmg） 
2. Vagrant（vagrant_1.8.5.dmg）， 安装

****
### 常用 vagrant 命令
```
vagrant box list 查看目前已有的box
vagrant box add 新增加一个box 需要在下载好的box(基于该box生成)的路径下执行，或者指定box的路径 如 vagrant box add myProject ubuntu16.01.box
vagrant box remove 删除指定的box
vagrant init  初始化配置文件 vagrantfile
vagrant up 启动虚拟机
vagrant ssh ssh登录虚拟机
vagrant suspend 挂起虚拟机
vagrant reload 重启虚拟机
vagrant halt 关闭虚拟机
vagrant status 虚拟机状态
vagrant destroy 删除虚拟机
```


实战
目标：
生成一个box
box镜像默认配置好 LAMP & LNMP
Yii2，TP5，Laravel 可以直接运行
****	
## 步骤：
### 1. 初始化并启动虚拟机

- VirtualBox 5.1.8
下载地址 https://www.virtualbox.org/wiki/Download_Old_Builds_5_1

- Vagrant 1.8.6
下载地址：https://releases.hashicorp.com/vagrant/1.8.6/
切记根据自己的操作系统下载，同时分32位和64位

- Windows 额外工作
>可能需要配置环境变;
安装Xshell命令行工具;
注意，一定要开启 VT-x/AMD-V 硬件加速


- 下载 ubuntu1404.box
- 首先运行 vagrant box list 看当前所有的box
- 然后在ubuntu1404.box 所在的路径执行 vagrant box add ubuntu1404（这个名字随意起） ubuntu1404.box
- 再次执行 vagrant box list 看是否添加成功
- 创建一个目录，在该目录中执行 vagrant init ubuntu1404（镜像的名称）
- 在该目录下就会生成一个名为 Vagrantfile 的配置文件
- 使用编辑器打开该配置文件，可以看到第15行，box的名称为刚才镜像的名称
- vagrant up 启动虚拟机，可以在virtualbox客户端看到启动的过程
- vagrant ssh 进入虚拟机

### 2. 替换源(使用阿里云的镜像，不用翻墙)
备份
```sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak```

编辑
文件内容为：
```
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```

更新源(**这里卡住了**)
```sudo apt-get update```

这部分有点坑，反正我能翻墙，不用它了


### 3. 安装 nginx
1. 首先进入虚拟机，查看 nginx 是否可安装
```apt-cache search nginx```
2. 安装 nginx
```sudo apt-get install nginx```
3. 查看是否安装成功
```nginx -v```
4. 测试 nginx 能否得到请求并正确响应
```curl -I 'http://127.0.0.1'```
```curl http://www.baidu.com```

### 4. 安装 apache2
1. 安装 nginx
```sudo apt-get install apache2```
3. 查看是否安装成功
```apache2 -v```
4. 测试 nginx 能否得到请求并正确响应 必须先停止nginx
```
sudo /etc/init.d/nginx stop
curl -I 'http://127.0.0.1'
curl -I 'http://www.baidu.com'
sudo /etc/init.d/apache2 start
curl -I 'http://127.0.0.1'
```

### 5. 安装 mysql
```
sudo apt-get install mysql-server
sudo apt-get install mysql-client
mysql -uroot -p
select version();
```

### 6. 安装 PHP
```
sudo apt-get install php5-cli
```

### 7. 安装 扩展
```
sudo apt-get install php5-mcrypt php5-mysql php5-gd
```

### 8. 安装 apache2 的 PHP 模块
```
sudo apt-get install libapache2-mod-php5
```

### 8. 安装 nginx fastcgi
fastcgi  fpm 是一个进程池，来管理PHP的进程(cgi)
```
sudo apt-get install php5-cgi php5-fpm
// 查询软件启动状态
ps -ef | grep apache
```

### 9. 更改端口
apache 和 nginx 默认都是监听 80 端口
```
// 更改 Apache 的监听端口为8888
sudo vim /etc/apache2/ports.conf 
listen 8888 保存后重启apache
```
这时候，监听的端口不同，两个server就可以同时运行，可以查看启动状态

验证是否可以访问
```
curl -I "http://127.0.0.1:80"
curl -I "http://127.0.0.1:8888"
```
按需求来，一般不会两个同时安装。

### 10. 通过浏览器访问虚拟机
```
// 挂起虚拟机
vagrant suspend
// 打开virtualbox，选中要操作的虚拟机-setting-network-advance-port forwarding
```

![](http://upload-images.jianshu.io/upload_images/693141-3ae6d25152d5914f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
配置完成后，保存，启动虚拟机。

8888端口，访问的是虚拟机里面的80端口，即nginx

8889端口，访问的是虚拟机里面的8888端口，即Apache

在浏览器中访问虚拟机
```
127.0.0.1:8888
127.0.0.1:8889
```
****
## vagrant 高级功能
进入当前虚拟机目录， 这样所有操作都是针对当前的虚拟机进行的操作。
### 端口转发
上述手动的配置，重启虚拟机之后自动消失。

vagrant每次重启的时候，都会按照自己的配置文件进行设置，所以手动更改的配置无效。

参考官网的示例是最好的学习方式。

修改本虚拟机的配置文件 Vagrantfile
```
config.vm.network "forwarded_port", guest:80 (虚拟机里的端口), host:8888 （主机里的端口）
config.vm.network "forwarded_port", guest:8888, host:8889
```
保存后重启虚拟机

### 网络设置

#### 1. 公有IP
公有IP不能创建nfs共享目录

IP段必须和主机（host 自己的电脑，不是虚拟机guest）的IP段一致，且不能与其他IP冲突。
#### 2. 私有IP
只能本机和虚拟机之间，以及虚拟机与虚拟机之间进行通讯。一般都使用私有IP
```
config.vm.network "private_network", ip:"192.168.11.11"
```
这样，可以直接在浏览器中输入IP来访问虚拟机的webserver

### 共享目录
一般共享代码目录，本地修改可以及时同步到虚拟机调试。

NFS方式
```
config.vm.synced_folder "/Users/ZouZhipeng/mooc_vagrant/", "/home/www/" (该目录如果不存在，会自动创建), :nfs => true
```
![](http://upload-images.jianshu.io/upload_images/693141-d8da821808b2e649.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/693141-1bd408cef572074d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



****
## vagrant 配置 ThinkPHP5
### 1. 安装git
```
sudo apt-get install git
```
### 2. 克隆代码
新建一个目录
```
git clone https://github.com/top-think/think tp5
```
切换到tp5目录下，再克隆核心框架仓库
```
cd tp5 && git clone https://github.com/top-think/framework thinkphp
```
### 3. nginx 配置文件（/etc/nginx/nginx.conf）详解
下面两行有一个包含的命令
```
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

也就是说，所有在conf.d目录下以.conf为后缀的文件，以及所有在sites-enabled目录下的文件，都可以自动加载到nginx的配置文件。这样不会影响到主配置文件。

新建一个配置文件 cd conf.d && sudo touch tp5.conf：

详解版
```
server{ // 所有nginx虚拟机的配置都是以 server 变量开始的
	server_name tp5.zzp.com; // 虚拟机域名
	root /home/www/tp5/public; // 代码路径 入口文件地址
	index index.php index.html; // 默认的索引文件
	location / {  // 
		if (-f $request_filename) { //如果访问的文件存在，直接跳转，不执行这里
			break;
		}
		if (!-e $request_filename) { // 如果访问的文件不存在 正则表达式（任何内容） 重定向到入口文件，参数为前面的正则表达式$1 last 表示不再走了
			rewrite ^/(.*)/$ /index.php/$1 last;
			break;
		}
	}

	location ~ \.php { // 任何路径，如果是以.php结尾的，包括前面的index.php
		set $script $uri;
		set $path_info "";
		if ($uri ~ "^(.+\.php)(/.+)") { // 如果路由符合这个规则
			set $script $1;
			set $path_info $2;
		} 
		include fastcgi_params; // 包含fastcgi参数
		// 需手动设置pathInfo类型的路由
		// 多传递一个参数，并解析出来
		fastcgi_index index.php?IF_REWRITE=1;
		fastcgi_param PATH_INFO $path_info;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_param SCRIPT_NAME $script;
		fastcgi_pass 127.0.0.1:9000; // fastcgi 端口
		try_files $uri = 404; // 如果文件不存在，直接404
	}
}
```

copy版
```
    server {
        charset utf-8;
        client_max_body_size 128M;
        listen 80;
        server_name tp5.imooc.test;
    
        root  /home/www/mooc/vagrant/phpmvc/tp5/public;
        index  index.php;
    
        location ~* \.(eot|otf|ttf|woff)$ {
            add_header Access-Control-Allow-Origin *;
        }
    
        
        location / {
            index    index.html index.php;
            if ( -f $request_filename) {
                break;
            }

            if ( !-e $request_filename) {
                rewrite ^/(.*)$ /index.php/$1 last;
                break;
            }
        }
    
        location ~ \.php {
        	set $script $uri;
            set $path_info "";
            if ($uri ~ "^(.+\.php)(/.+)") {
                set $script $1;
                set $path_info $2;
            }
            include   fastcgi_params;
            fastcgi_index    index.php?IF_REWRITE=1;
        	fastcgi_pass   127.0.0.1:9000;
        	fastcgi_param    PATH_INFO    $path_info;
            fastcgi_param    SCRIPT_FILENAME    $document_root$fastcgi_script_name;
            fastcgi_param    SCRIPT_NAME    $script;
            try_files $uri =404;
        }
    
    }
```
 
在 /etc/hosts 文件中绑定上面配置的域名 其实就是自定义的DNS服务
```192.168.11.11 tp5.zzp.com```

然后再浏览器中访问，发现报错404。

#### 排错
排错的方式就是查看日志，首先找到日志存放的位置。主配置文件nginx.conf中可以找到。
```
##
# Logging Settings
##
access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;
```
首先看访问日志，如果有变化说明访问进来了。
```
tail -f /var/log/nginx/access.log
```
如果没权限，先修改访问日志的权限为777

再次在浏览器中访问，访问日志有记录。然后查看错误日志。
```
tail -f /var/log/nginx/error.log
```
同样需要先修改权限。

如果错误为 connection refused，如何排查。

```
cd /etc/php5/fpm/pool.d
sudo vim www.conf 搜索listen
找到
listen = /var/run/php5-fpm.sock
这是监听域模式的方式，改成端口方式进行监听
listen = 127.0.0.1：9000 
端口方式比域模式更加稳定
修改后重启fpm服务
sudo service php5-fpm restart
sudo /etc/init.d/php5-fpm restart 这两行一样
```
再次通过浏览器访问，终于通了。

### 配置apache
```
cd /etc/apache2
vim apache2.conf
打开配置文件，最下面可以看到
# Include the virtual host configurations:
IncludeOptional sites-enabled/*.conf
找到子配置文件的目录
cd sites-enabled
sudo touch tp5.conf
```
```
<VirtualHost *:8888> // xml 形式 端口为8888
	ServerName tp5.zzp.com
	DocumentRoot /home/www/tp5/public
</VirtualHost>
```
然后重启apache

浏览器中访问 tp5.zzp.com:8888  返回403错误

#### 解决方式
看错误日志
```
sudo chmod -R 755 /var/log/apache2
tail -f error.log
client denied by server configuration
一般是Apache的配置错误导致的
sudo vim /etc/apache2/apache2.conf

<Directory />
        Options FollowSymLinks
        AllowOverride None
        Require all denied // 把这行注释掉即可,然后重启apache
</Directory>
```
TP 的路由解决方式已经非常落后，给webserver 的配置带来了很多麻烦，看看就好。

#### 开启Apache的rewrite功能

```
sudo a2enmod rewrite
```
在Apache的配置文件中还要修改

```
sudo vim /etc/apache2/apache2.conf
找到 Directory 的 AllowOverride, 改为All
```

## 2. vagrant 安装yii2 & Laravel5
### 1. 下载yii-basic 的压缩包，解压到目标目录
### 2. 配置nginx运行环境
```
cd /etc/nginx/conf.d
sudo touch yii2.conf
sudo vim yii2.conf
sudo service nginx restart

server{
    server_name yii.zzp.com;
    root /home/www/yii/web;
    index index.php;
    location / {
      try_files $uri $uri/ /index.php?$args;
  }
    location ~ \.php$ {
      include fastcgi_params;
      fastcgi_pass 127.0.0.1:9000;
      try_files $uri = 404;
  }
}

别忘了 hosts 在浏览器中就可以访问了
```
### 3. 配置Apache
```
cd /etc/apache2/sites-enabled
sudo touch yii.conf
sudo vim yii.conf

<VirtualHost *:8888>
  ServerName yii.zzp.com
  DocumentRoot /home/www/yii/web
</VirtualHost>

重启apache 在浏览器中就可以访问了
```

### 4. 下载并解压 laravel安装包
配置基本和yii一致
```
server{
    server_name laravel.zzp.com;
    root /home/www/laravel/public;
    index index.php;
    location / {
      try_files $uri $uri/ /index.php?$args;
  }
    location ~ \.php$ {
      include fastcgi_params;
      fastcgi_pass 127.0.0.1:9000;
      try_files $uri = 404;
  }
}
```
```
<VirtualHost *:8888>
  ServerName laravel.zzp.com
  DocumentRoot /home/www/laravel/public
</VirtualHost>
```

## 3. vagrant  虚拟机优化
### 虚拟机名字 内存和CPU
修改vagrantfile并重启虚拟机
```
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
      vb.memory = "1024"
      vb.cpus = 2
      vb.name = "ubuntu_mooc"
  end
```
### 虚拟机主机名
```
config.vm.hostname = "mooc"
```
查看虚拟机内容 ```free -m```
CPU ```top``` 再按1查看，q退出


### 优化nginx和apache 同步目录的问题 修改后文件可及时同步

![](http://upload-images.jianshu.io/upload_images/693141-368f8373feeaa483.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

****
## 打包分发环境
### 打包 
需先关闭虚拟机 vagrant halt
```
vagrant package --output xxx.box
vagrant package --output xxx.box --base 虚拟机名
```
### 升级
老用户：使用Vagrantfile进行升级；
```
config.vm.provision "shell", inline: <<-SHELL
apt-get install -y redis-server (-y 表示强制安装)
vagrant reload --provision
```
新用户：直接重新打包分发给他
```
vagrant box add new xxx.box
新建一个目录
vagrant box list
vagrant init new
```

配置文件里面的vb.gui可以查看虚拟机启动时候的情况，可用于错误调试。

打包的时候最好关闭网络配置




```
// Vagrantfile 配置文件所有内容
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu1404"
  config.vm.hostname = "mooc"
  config.vm.network "forwarded_port", guest: 80, host: 8888 ,id: 'nginx'
  config.vm.network "forwarded_port", guest: 8888, host: 8889 ,id: 'apache'
  config.vm.network "private_network", ip: "192.168.11.11",auto_config: true
  config.vm.synced_folder "/Users/ZouZhipeng/www/mooc_vagrant/", "/home/www", :nfs => true
  #config.vm.synced_folder "/Users/vincent/code/", "/home/www", :nfs => true

  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
      vb.memory = "1024"
      vb.cpus = 2
      vb.name = "ubuntu_mooc"
  end


  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```


