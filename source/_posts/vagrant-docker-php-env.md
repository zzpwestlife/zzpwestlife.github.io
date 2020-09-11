---
title: vagrant-docker-php-env
date: 2018-04-07 17:12:51
tags: docker
---
# vagrant-docker-php-env
## Mac 系统下 vagrant-docker-php-env


### 折腾了两天，备份一下

在 https://github.com/kasperisager/php-dockerized 的基础上修改，感谢

<!-- more -->

### 1. 下载配置文件 

```
git clone https://github.com/zzpwestlife/vagrant-docker-php-env.git
```

按需修改 Vagrantfile 的虚拟机 ip 和 共享文件目录

```
:eth1 => "192.168.10.10", // 这一行是虚拟机的 ip，安装完成后使用浏览器访问该 ip 即可
config.vm.synced_folder "/Users/ZouZhipeng/www", "/home/vagrant/www" // 这里是共享目录，前者为本机目录，后者为虚拟机目录
```

### 2. 进入该目录

```
cd vagrant-docker-php-env
```

并将准备好的 nginx 配置放入 php-dockerized/sites/ 目录中

### 3. 安装 vagrant 虚拟机

```
vagrant up
```

如果没有安装 vagrant，需要提前安装

这个过程可能会比较漫长，建议开启代理

### 4. 安装完成后进入虚拟机

```
vagrant status
vagrant ssh
```

其他命令，如停止、重启、删除等
```
vagrant halt
vagrant reload
vagrant destroy
```


### 5. 进入 docker-compose.yml 所在的目录

```
cd /home/vagrant/www/docker/php-dockerized
```


将 daemon.json 复制至 /etc/docker 目录下

```
sudo cp ../daemon.json /etc/docker/
```

这个配置文件替换了 docker 镜像的下载源，使用国内的下载源速度会快些

### 6. 构建镜像

```
docker-compose build
```

### 7. 生成容器并启动

```
docker-compose up -d
```

第一次生成也需要不少时间

### 8. 在本机增加 hosts 映射

```
vim /etc/hosts
192.168.10.10 xb.insurance.com
```
通过浏览器访问

### 9. 其他命令

```
# 停止所有容器
docker-compose stop
# 停止并删除容器
docker-compose down
# 进入某个容器
docker-compose exec containerName bash
# 每次修改配置都需要重新 build 一下
docker-compose build containerName
```

> 注意：

本机目录、虚拟机目录、容器目录三者关系要搞清楚

nginx 和 php-fpm 使用 socket 通信，需要设置多个位置

