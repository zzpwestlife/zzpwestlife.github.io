---
title: 全新离线安装 Laravel Homestead 记录
date: 2016-12-03
tags: laravel
---

![](http://upload-images.jianshu.io/upload_images/693141-3263c5e58700a808.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 之前安装的homestead系统版本为Ubuntu14.04，现业务需要，需升级为16.04，在虚拟机中直接升级完成后，无法启动。正好趁这个机会把所有需要升级的都升级一遍，全新安装并Laravel运行环境。
<!--more-->

## 1. 准备工作
1. 备份好数据后，彻底删除原来的运行环境，包括Homestead文件夹，虚拟机目录，.homestead 文件夹，box（```vagrant box remove boxname```）等。
2. 下载并安装 [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
3. 下载并安装 [vagrant](https://www.vagrantup.com/downloads.html)
4. 下载homestead离线安装包 [virtualbox](https://atlas.hashicorp.com/laravel/boxes/homestead/versions/2.0.0/providers/virtualbox.box)，版本号的数字自行修改，如2.0.1 
5. virtualbox 可启动，命令行中 ```vagrant -v``` 显示vagrant版本号，准备工作完成。


## 2. 开始安装
[官方文档](http://www.golaravel.com/laravel/docs/5.0/homestead/)给出的安装方式为在线安装，蛋疼的网速想要在短时间内安装成功非常困难，所以这里采用离线安装方式。
1. 新建一个目录，我这里命名为 vagrant，将准备工作下载好的 virtualbox.box 拷贝到该目录下
2. 添加box，这里步骤2与步骤3实现的效果一致，但步骤2添加成功后可能会有更新问题，**推荐跳过此步骤直接执行步骤3**.
```vagrant box list```查看目前系统中已有的box，新加的box尽量不要与原有的box重名。
    ```vagrant vagrant box add  laravel/homestead virtualbox.box```，如果box文件在当前目录下，直接添加文件即可，如果不在，需要写明全路径。
    
    看到success添加盒子就成功了。
3. 在virtualbox.box镜像所在目录创建metadata.json，输入以下内容
    ```
    {
        "name": "laravel/homestead",
        "versions": [{
            "version": "2.0.0",
            "providers": [{
                "name": "virtualbox",
                "url": "file://virtualbox.box"
            }]
        }]
    }
    ```
    其中，url最好写box的全路径。然后输入以下命令添加box
    ```
    vagrant box add metadata.json
    ```
    查看已安装的box
    ```
    vagrant box list
    ```


## 3. 克隆官方仓库
homestead，vagrant，virtualbox之间的关系梳理
1. homestead镜像就是laravel官方为了方便开发者，将一系列的开发环境、软件打包成一个镜像供大家使用，当前homestead包含[以下内容](https://laravel.com/docs/5.4/homestead)：Ubuntu 16.04，Git，PHP 7.1，Nginx，MySQL，Postgres，Composer，Node (With Yarn, Bower, Grunt, Gulp) 等。
2. vagrant可以看作是对virtualbox或vmware的一个高级封装，本质就是调用了一些virtualbox和vmware开放出来的api
3. homestead git仓库呢则是laravel官方对于homestead虚拟机的一些配置文件，里面有一些方便的Linux脚本

clone [版本库](https://github.com/laravel/homestead)
```
git clone https://github.com/laravel/homestead.git Homestead
```

## 4. 配置 Homestead
1. 进入到clone的目录中运行 init.sh，这样就在家目录中生成一个.homestead 的目录，里面包含homestead虚拟机的配置文件 Homestead.yaml (homestead在github上的版本已更新到5.1，有一些文件发生了变动。最主要的变动是当运行init.bat或init.sh时候，几个文件将不再复制到家目录的.homestead文件，而是直接被复制到homestead项目的根目录中。原先家目录下的.homestead可以直接删掉了！)
2. 修改该文件
    ```
    ip: "192.168.10.10"
    memory: 2048
    cpus: 1
    provider: virtualbox
    
    authorize: ~/.ssh/id_rsa.pub
    
    keys:
        - ~/.ssh/id_rsa
    
    folders:
        - map: /myCodeDir
          to: /home/vagrant/www
    
    sites:
        - map: homestead.app
          to: /home/vagrant/www/Laravel/public
    
    databases:
        - homestead
    
    # blackfire:
    #     - id: foo
    #       token: bar
    #       client-id: foo
    #       client-token: bar
    
    # ports:
    #     - send: 50000
    #       to: 5000
    #     - send: 7777
    #       to: 777
    #       protocol: udp
    ```
    主要需要改动的就是 folders这一项，将本机的一个目录共享给虚拟机。
    rsa 公钥私钥路径一定要对，不然无法启动，没有的话通过git bash或其他Linux环境输入以下命令生成
    ```
    ssh-keygen -t rsa -C "vagrant@homestead"
    ```

## 5. 启动 homestead
1. 进入到homestead目录(git clone的那个) ```vagrant up```，耐心等待一会就可以。
2. 修改本地的hosts文件，添加homestead虚拟机的DNS
3. 没有报错就正常启动了。 输入命令 ```vagrant ssh```进入虚拟机。
4. 升级，配置nginx等。

## 6. 参考资料
1. [离线安装&配置laravel开发环境homestead
](https://www.alwayscoder.com/offline-install-laravel-homestead/)
2. [私钥问题](http://stackoverflow.com/questions/22922891/vagrant-ssh-authentication-failure)
3. [连接超时问题](http://stackoverflow.com/questions/22575261/vagrant-stuck-connection-timeout-retrying)
4. [Vagrant入门](http://www.cnblogs.com/davenkin/p/vagrant-virtualbox.html)


