---
title: Shell-脚本学习记录（1）
date: 2017-02-22
tags: shell
description: 这是我在慕课网《Shell典型应用之主控脚本实现》的学习记录，方便自己以后查找。希望对其他读者也能有些帮助。
---

![](http://upload-images.jianshu.io/upload_images/693141-8b4a409ba533a5ac.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 这是我在慕课网[《Shell典型应用之主控脚本实现》](http://www.imooc.com/learn/522)的学习记录，方便自己以后查找。希望对其他读者也能有些帮助。

<!--more-->

# 第一部分 主控文件
## 1. vim 编辑器的设置
1. 临时性的设置
在末行模式进行的设置，当次有效
2. 永久设置 修改vimrc/.vimrc文件
    ```
    # 语法高亮
    syntax on
    # 显示行号
    set number
    # 自动缩进
    set autoindent
    set cindent
    # 自动加入文件头
    ```
    ![](http://upload-images.jianshu.io/upload_images/693141-70a7f46e1f5d6174.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
## 2. shell 高亮显示
```
echo -e 终端颜色 + 显示内容 + 结束后颜色
echo -e "\e[1;30m Hello \e[1;0m"
# echo -e(扩展输出) "\e(颜色设置使用\e)[1(1为设置终端颜色，0为不设置);
# 30m(颜色) Hello \e(闭合)[1;0m(结束后的颜色)"(所有内容包含在双引号中)
echo -e "\e[1;30m Hello " $(tput sgr0)
# $(tput sgr0)的作用是初始化输出的终端，使其颜色恢复为默认值
```
效果图

![](http://upload-images.jianshu.io/upload_images/693141-b86151289bd439b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3. shell 中的关联数组
```
# 声明关联数组
declare -A asso_arr1
```

## 4. ubuntu的坑
ubuntu的默认shell由bash改为了dash，上述脚本输出时会出现两个错误。1，declare not found; 2，echo 后面的颜色配置内容都给输出。

三种解决方法：
1. 执行时由```sh build.sh```变成```bash build.sh```
2. ```ln -s /bin/bash /bin/sh -f```
3. ```sudo dpkg-reconfigure dash``` 进行配置，道理同（2）


主控文件所有内容及注释 monitor_main.sh

```
#!/bin/bash
resettem=$(tput sgr0)
# 定义关联数组存储 键：数字,脚本号码   值：脚本名
DECLARE -A ssharray
i=0
numbers=""

# 使用for循环遍历所有子功能shell并输出(需要排除本文件) 注意单引号和双引号的使用
for script_file in `ls -I "monitor_main.sh" ./`
do
    echo -e "\e[1;35m" "The Script:" ${i} '==>' ${ressettem} ${script_file}
    # 关联数组赋值
    ssharray[$i]=${script_file}
    # echo ${ssharray[$i]}
    numbers="${numbers} | $i"
    i=$((i+1))
done
# echo ${numbers}
# 写一个无限循环
while true
do
  # 输入提示
  read -p "Please input a number [ ${numbers} ]:"  execshell
  # echo ${execshell}
  # 如果输入内容不是数字，中断
  if [[ ! ${execshell} =~ ^[0-9]+ ]];  then
     exit 0
  fi

  # 如果输入正确，执行对应的脚本
  /bin/bash ./${ssharray[$execshell]}
done
```


# 第二部分 系统信息及运行状态获取
## 1. 系统信息所有内容及注释 system_monitor.sh
```
#!/bin/bash
# 清屏
clear
# $# 可提取位置参数个数 注意 [] 与内容之间都要有空格
if [[ $# -eq  0 ]]
then
# 定义变量
reset_terminal=$(tput sgr0)
# check OS type
os=$(uname -o)
echo -e '\e[1;32m' "Operating System Type: " $reset_terminal $os
# check OS release version and name
os_name=$(cat /etc/issue)
# os_name=$(cat /etc/issue|grep -e "Server")
echo -e '\e[1;32m' "release version and name: " $reset_terminal $os_name
# check architecture
architecture=$(uname -m)
echo -e '\e[1;32m' "architecture Type: " $reset_terminal $architecture
# check kernel release
kernel=$(uname -r);
echo -e '\e[1;32m' "kernel Type: " $reset_terminal $kernel
# check hostname
hostname=$(uname -n)
echo -e '\e[1;32m' "hostname: " $reset_terminal $hostname
# hostname=$(hostname)
# hostname=$(echo $HOSTNAME)

# check internal IP
internalIP=$(hostname -I)
echo -e '\e[1;32m' "internalIP: " $reset_terminal $internalIP
# check external IP
externalIP=$(curl -s http://ipecho.net/plain)
echo -e '\e[1;32m' "externalIP: " $reset_terminal $externalIP
# check DNS
# -E 支持元字符  加边界匹配，匹配以<nameserver开头，后面跟任意多个空格的内容
# 使用awk做列筛选，$NF 表示以空格作为分隔符，打印出最后一列
# 注意单双引号的使用
DNS=$(cat /etc/resolv.conf |grep -E "\<nameserver[ ]+" | awk '{print $NF}')
echo -e '\e[1;32m' "DNS: " $reset_terminal $DNS
# check internet connection status
# ping -c 表示次数
# 输出到空设备  &>/dev/null
ping -c 2 baidu.com &>/dev/null && echo "Internet connected" || echo "Internet disconnected"
# check LoggedIn users
who>/tmp/who
echo -e '\e[1;32m' "LoggedIn users: " $reset_terminal && cat /tmp/who
# 用完及时删除
rm -rf /tmp/who
fi
```
效果图

![](http://upload-images.jianshu.io/upload_images/693141-30d101e3970871cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2. 系统监控脚本
1. 操作系统使用内存与应用使用内存的区别
![](http://upload-images.jianshu.io/upload_images/693141-1fdb300ae6d72455.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
系统使用内容 = total - free
应用使用内容 = total - (free + cached + buffers)
```

2. 内存中cache和buffer 区别

![](http://upload-images.jianshu.io/upload_images/693141-7fbc5e89dfe596d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

cache主要用来缓存打开过的文件，buffer用于缓存目录项，inode节点（文件索引头）。

除了使用 free -m 来获取内存外，还可以使用读取文件的方式来获取内容，文件位于 /proc/meminfo
```
awk '/MemTotal/{total=$2}/MemFree/{free=$2}END{print (total-free)/1024}' /proc/meminfo
```
解释： 使用awk来进行模式匹配，首先 匹配 MemTotal，第二列的值赋值给变量total；匹配MemFree，第二列的值赋值给free。使用END结束匹配，输出计算结果，并以MB单位显示。

```
############################################################
# 内存使用
system_memory_usage=$(awk '/MemTotal/{total=$2}/MemFree/{free=$2}END{print (total-free)/1024}' /proc/meminfo)
echo -e '\e[32m' "system memory usage:" $reset_terminal $system_memory_usage
# 有两个cache，匹配第一个
app_memory_usage=$(awk '/MemTotal/{total=$2}/MemFree/{free=$2}/^Cached/{cached=$2}/Buffers/{buffers=$2}END{print (total-free-cached-buffers)/1024}' /proc/meminfo)
echo -e '\e[32m' "apps memory usage:" $reset_terminal $app_memory_usage
```

## 3. CPU 负载

![](http://upload-images.jianshu.io/upload_images/693141-b08caa30fe4cfcd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以读取 /proc/meminfo 文件，也可以使用 top 命令。
```
top -n 1 -b | grep "load average" | awk '{print $10  $11  $12}'
```
解释： -n 表示刷新次数，一次即可。  -b表示更完整地显示信息。 awk 进行列匹配，取出第10，11，12 列的数值。

![](http://upload-images.jianshu.io/upload_images/693141-04d1c54659ab212b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 磁盘容量 使用量
```
df -h | grep -vE 'Filesystem|tmpfs' | awk '{print $1 " " $5}'
```
解释： v表示反选，E表示元字符，匹配到的内容将别忽略，即取出除匹配到的内容之外的所有内容。

```
###########################################################
# CPU
loadaverage=$(top -n 1 -b | grep "load average" | awk '{print $10  $11  $12}')
echo -e '\e[32m' "load averages " $reset_terminal $loadaverage
diskaverage=$(df -h | grep -vE 'Filesystem|tmpfs' | awk '{print $1 " " $5}')
echo -e '\e[32m' "disk averages " $reset_terminal $diskaverage
```


