---
title: 《循序渐进学 Docker》学习 - part 1
date: 2017-06-26 13:04:29
tags: Docker
Categories: Docker
---

## 1. Docker 基础

Docker 工作流程：首先，要在 Linux 服务器上安装 Docker 软件包，并启动 Docker Daemon 守护进程。然后，就可以通过 Docker Client 端发送各种指令，Docker Daemon 守护进程执行完指令，向 Client 端返回结果。

Docker 分层：把一个应用分为任意多个层，比如操作系统是第一层，依赖的库和第三方软件是第二层，应用的软件包和配置文件是第三层。如果两个应用有相同的底层，就可以共享这些层。

<!--more-->

Docker 写时复制/写时拷贝策略：Docker 层次是有优先级的，上层和下层有相同的文件和配置时，上层覆盖下层，数据以上层的数据为准。给每个应用一个优先级最高的空白层，如果需要修改下层的文件，就把这个文件拷贝到这个优先级最高的空白层进行修改，保证下层的文件不做任何改变。这样，对于该应用来说，文件已经修改成功，而从其他应用的角度来看，文件没有发生任何改变。


Docker 内核虚拟化技术：与宿主机运行在相同的 Linux 内核，不需要指令级模拟，性能消耗非常小，是非常轻量化的虚拟化容器。


Docker 具有版本控制和增量更新功能，类似 git。

生产环境的机器上可以同时缓存应用的多个版本镜像，如果新发布的版本有问题，可以快速切换回原来的版本。

因为 Docker 的应用镜像包含应用运行需要的所有软件包、配置和操作系统，所以开发者打包好 Docker 镜像，测试和运维人员从私有仓库下拉，不需要做任何修改，就可以运行起来，并保证和开发者运行的环境完全一致，一处编译，到处运行。


## 2. Docker 快速搭建 WordPress 博客网站

只需要两条命令

```
$ docker run --name db --env MYSQL_ROOT_PASSWORD=example -d mariadb

$ docker run --name myWordPress --link db:mysql -p 8080:80 -d wordpress
```

分析 1：

> `docker run` 是一条 Docker 命令，后面的所有内容都是 Docker 指令的参数。

> 这条指令的含义（从后向前）是启动一个 mariadb 数据库，并且让这个数据库在后台运行。通过环境变量设置数据库管理员root的密码，并给起了一个唯一的名字 db 进行标识。

`docker run` 首先会从本机检查有没有 mariadb 程序，如果没有，就会从 Docker Hub 搜索并下载该程序， Docker Hub 就像 iPhone 是哪个的 App Store。

分析 2：

> WordPress 是把博客和个性化信息存储到数据库，所以需要和数据库建立连接。`--link db:mysql` 参数，把 WordPress 和数据库建立起了连接。

> WordPress 是通过监听 Apache 的 80 端口对外提供服务。通过 `-p 8080:80` 参数，把原服务的 80
 端口映射到 8080，这样就可以通过访问 8080 端口来访问服务。URL为 127.0.0.1:8080
 



