---
title: git 的几个小问题
date: 2017-04-11
tags: git
---
## 1. `git blame` 指定行或范围


```
$ git blame filename -L150,+11
```


这里 `-L150,11` 表示“只看150-160行”。

<!-- more -->

## 2. `git cherry-pick` 合并某一次提交到另一分支

经常地，在错误的分支里进行了想要的提交，这时需要将本次提交合并到正确的分支。

```
$ git checkout 目标分支名
$ git cherry-pick 想要合并的提交
```



