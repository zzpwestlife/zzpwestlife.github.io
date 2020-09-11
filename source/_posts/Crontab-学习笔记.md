---
title: Crontab 学习笔记
date: 2017-03-03
tags: crontab
---

![](http://upload-images.jianshu.io/upload_images/693141-b997419bb7de3810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是慕课网[《Linux中的计划任务之Crontab》](http://www.imooc.com/learn/216)的学习记录，方便以后查阅。

> 从定时重复的工作中解脱出来

<!--more-->

## 1. Crontab 是什么？
Crontab 是一个用于设置**周期性**被执行的任务的工具。

1. Cron Job：被周期性执行的任务
2. Cron Table：周期性执行的任务列表

![](http://upload-images.jianshu.io/upload_images/693141-b859093fa8a16912.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. 安装并检查Crontab服务
1. 检查Crontab工具是否安装：```crontab -l``` 
2. 检查crond 服务是否启动：```service crond status```
3. 安装cron
```
CentOS
yum install vixie-cron
yum install crontabs
Ubuntu
sudo apt-get update
sudo apt-get install cron
```

## 3. 简单示例
```
crontab -e 进入编辑模式

添加一行

*/1 * * * * date >> /tmp/date.log
每分钟将当前日期时间写入日志文件

tail -f /tmp/date.log （动态刷新）
tail -3 /tmp/date.log (显示最后3行)
```

## 4. Crontab 的基本组成
1. 配置文件：文件方式设置定时任务
2. 系统服务CROND：每分钟都会从配置文件刷新定时任务、执行定时任务
3. 配置工具crontab：调整定时任务

### 1. 配置文件格式

> 分钟0-59

> 小时0-23

> 日期1-31

> 月份1-12

> 星期0-7（0和7都表示星期日）

![](http://upload-images.jianshu.io/upload_images/693141-a1d1e4b3b0a62fb5.gif?imageMogr2/auto-orient/strip)

示例：

每晚21：30重启apache
```
30 21 * * * service httpd restart
```
每月1、10、22号4：34重启apache（离散）
```
34 4 1,10,22 * * service httpd restart
```

每月1-10号4：34重启apache （连续）
```
34 4 1-22 * * service httpd restart
```
每2分钟重启apache
```
*/2 * * * * service httpd restart (偶数分钟)
1-59/2 * * * * service httpd restart （奇数分钟）
```
晚上11点到早晨7点重启apache
```
0 23-7/1 * * * service httpd restart (分钟要指定)
```

晚上11点到早晨7点每隔30分钟重启apache
```
0,30 23-7/1 * * * service httpd restart
0-59/30 23-7/1 * * * service httpd restart
```
小结：

1. \* 表示任何时候都匹配。
2. 可以用"A,B,C" 表示A或者B或者C时执行命令
3. 可以用"A-B"表示A到B之间时执行命令
4. 可以用"*/A"表示每A分钟（小时等）执行一次命令


## 5. Crontab 工具的使用

```
crontab -help
crontab -l
crontab -e
crontab -e -u cron
tail -f /var/log/cron 查看计划任务的执行情况
```
如果没有指定用户，显示的就是当前登录用户自己的计划任务。


## 6. Crontab 配置文件
位于 ```/etc/crontab``` 目录下
![](http://upload-images.jianshu.io/upload_images/693141-1423dc95ddf1a9e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

crontab 在载入配置文件的时候，会把cron.d 下面的sysstat文件里面的配置信息载入成为root用户的计划任务。

crontab -l/-e 不能查看和编辑系统级的计划任务，即 cron.d 目录下的文件，需要 cd 到该目录使用vi编辑。

crontab -l/-e 其实是操作的 /var/spool/cron/username 文件



## 7. 修改Crontab的默认编辑器为vi
在root的配置文件 .profile (/root/.profile)添加以下代码
```
EDITOR=vi; export EDITOR
```
然后，载入这个文件即可
```
source .profile
```

## 8. 几个坑
### 1. 环境变量
.bash_profile 中设置的环境变量并不会被Crontab捕获。

### 2. 第三和第五个域之间执行的是“或”操作
如四月第一个星期日早晨6：30执行脚本
```
30 6 1-7 4 0 /root/a.sh
```
上面写法的结果是：四月第一个星期的周一到周日（7次）或周日（一个月最少4次）早晨执行，总执行次数为10或11，并不是我们希望的结果。
```
30 6 1-7 4 * test `date +\%w` -eq 0 && /root/a.sh
```

```
date +%w 输出当前的星期，0-6
test 表示是否成功
echo $? 输出上一个命令成功与否，1表示失败，0表示成功
避免使用等号（用的话前后都加空格）进行判断，使用 -eq
&& 表示前面成功（0）后面才执行
% 在计划任务中要进行转义
```

### 3. 分钟设置
\* 表示所有都匹配，所以每小时，每天这样的情况要给小一级的单位设置一个默认值。
```
0 */2 * * * date
```

## 9. 综合案例
每30s执行一次
```
*/1 * * * * echo "1111"
*/1 * * * * sleep 30s; echo "1111" (分号表示无论前面的命令是否成功，后面都会执行)
```


