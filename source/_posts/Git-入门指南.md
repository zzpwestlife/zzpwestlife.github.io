---
title: Git 常用命令
date: 2016-11-07
tags: git
description: 创建版本库：新建一个空目录，cd 到该目录，输入```git init```即可把该目录变为 git 可以管理的仓库。
---

1. 创建版本库：新建一个空目录，cd 到该目录，输入```git init```即可把该目录变为 git 可以管理的仓库。

<!--more-->

2. 上一步会自动生成```.git```的目录，默认是隐藏的，可以通过``` ls -ah```显示，但里面的东西最好不要动。
3. 把文件添加到版本库：```git add yourfile.txt```
4. 把文件提交到仓库： ```git commit -m 'your message'```
5. 查看当前版本库状态：```git status```
6. 查看修改内容：```git diff```，没有问题后， ```add & commit```
7. 查看日志：时间由近至远 ```git log```
```git log --pretty=oneline```使版本 ID 和每次修改输入的内容在一行显示
8. git 中，```HEAD```指向当前版本，```HEAD^```指向上一个版本，```HEAD^^```指向上两个版本， ```HEAD-100```指向往上100个版本。
想要恢复到某一个版本：```git reset --hard HEAD-3```
后悔药：在命令行窗口中找到 commit id，```git reset --hard 3564223```，版本号没必要写全，前几位就可以了，Git会自动去找。这样就回退了版本，如果 add 了而没有 commit，用命令```git reset HEAD file```可以把暂存区的修改撤销掉（unstage），重新放回工作区。
如果已经关闭命令行窗口，commit id 可以通过```git reflog```找回。
9. 放弃本地工作区的修改，恢复到版本库或暂存区的状态。```git checkout -- yourfile.txt```，注意这里两个横向前后都要有空格。


