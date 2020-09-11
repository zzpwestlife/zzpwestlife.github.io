---
title: MySQL 知识整理
date: 2018-02-24 14:26:02
tags: MySQL
---

### mysql 关联更新

update 后面跟的表名是即将要使用到的表

```sql
update A,B set A.c1=b.c1, A.c2=b.c2 where A.id=B.id;
update A inner join B on A.id=b.id set A.c1=B.c1, A.c2=B.c2 where ...;
```
<!-- more -->

### 6 种关联查询
- 交叉连接 cross join: 笛卡尔积
    - `select * from A, B, (,C)`
    - `select * from A cross join B (cross join C)`
    - 没有任何关联条件，结果是笛卡尔积，结果集会很大，没有意义，很少使用
- 内连接 inner join
    - `select * from A,B where A.id=B.id`
    - `select * from A (inner) join B on A.id=B.id`
    - 多表中同时符合某种条件的数据记录的集合
    - 等值连接（`on A.id=B.id`）、不等值连接（`on A.id>B.id`）、自连接（`select * from A t1 inner join A t2 on t1.id=t2.pid`）
    - 可以缩写为 join
- 外连接 left join/right join
    - 左外连接： left outer join，以左表为主，先查询出左表，按照 on 后的关联条件匹配右表，没有匹配到的用 null 填充，可以简写为 left join
    - 右外连接 right outer join
- 联合查询 union, union all
    - `select * from A union select * from B union ...`
    - 多条 select 语句的结果集进行累加，union 前的结果为基准
    - 列数要相等，相同的记录行会合并
    - 如果使用 union all，则不会合并重复的记录行
    - union 效率高于 union all
- 全连接 full join
    - 只是一个概念，mysql 不支持
    - 可以使用 left join，union，right join 联合实现，`select * from A left join B on A.id=B.id union select * from A right join B on A.id=B.id`


### MySQL 查询优化
#### 1. 查找分享查询速度慢的原因

##### 记录慢查询日志
分析查询日志，不建议直接打开慢查询日志进行分析，可以使用 pt-query-digest 工具进行分析

##### 使用 show profile
`set profiling=1;` 开启，服务器上执行的所有语句都会监测消耗的时间，存到临时表中

``` sql
show profiles;
show profile for query 临时表中单条记录ID;
```

##### 使用 show status
`show status` 会返回一些计数器，`show global status` 查看服务器级别的所有计数

有时根据这些计数，可以猜测出哪些操作代价较高或者消耗时间多

##### 使用 show processlist
观察是否有大量进程处于不正常的状态或者特征

##### 使用 explain
分析单条 SQL 语句
`explain select * from A;`

#### 2. 优化查询过程中的数据访问
- 访问数据太多导致查询性能下降
- 确定应用程序是否在检索大量超过需要的数据，可能是太多行或列
- 确认 mysql 服务器是否在分析大量不必要的数据行

方案
- 查询不需要的记录，使用 limit
- 多表关联只返回所需要的列
- select * 不要用
- 重复查询相同的数据，可以缓存数据，下次直接读取缓存
- 使用索引覆盖扫描，把所有用的列都放到索引中，这样存储引擎不需要回表获取对应行就可以返回结果
- 改变数据库和表的结构，修改数据表范式（冗余字段降低范式，用空间换取时间）
- 重写 SQL 语句，让优化器可以以更优的方式执行查询

#### 3. 优化长难的查询语句
应当使用一个复杂查询还是多个简单查询？

MySQL 内部每秒能扫描内存中上百万行的数据，相比之下，响应数据给客户端就要慢得多

大多数情况下，使用尽可能少的查询是好的，但是有时将一个大的查询分解为多个小的查询也是很有必要的，这样可以方便地做缓存

##### 切分查询
将一个大的查询分为多个小的相同的查询

一次性删除 1000 万的数据比一次删除一万，暂停一会的方案更加损耗服务器开销。

##### 分解关联查询
- 可以将一条关联语句分解成多条 SQL 来执行
- 让缓存效率更高
- 执行单个查询可以减少锁的竞争
- 在应用层做关联可以更容易对数据库进行拆分


#### 4. 优化特定类型的查询语句

##### 优化 count(*)
- count(*) 中的 * 会忽略所有的列，直接统计所有列数，速度更快，因此不要使用 count(列名)
- MyISAM 中，没有任何 where 条件的 count(*) 非常快
- 当有 where 条件，MyISAM 的 count 统计也不一定比其他表引擎快
- 或者增加一张汇总表
- 缓存汇总信息

##### 优化关联查询
- 确认 on 或者 using 子句的列上有索引，如果没有要添加，否则会导致全表扫描
- 确保 group by 和 order by 中只有一个表中的列，这样 MySQL 才有可能使用索

##### 优化子查询
尽可能使用关联查询来替代

##### 优化 group by 和 distinct
- 均可使用索引来优化，是最有效的优化方法
- 关联查询中，使用标识列进行分组效率更高，即 group by 后面跟的列名是主键列或 auto_increment 列
- 如果不需要排序，进行 group by 时使用 order by null，MySQL 就不会再进行文件排序
- with rollup 超级聚合，尽量挪到应用程序处理，而不应该在数据库中处理

##### 优化 limit 分页
- limit 偏移量大的时候，查询效率较低
- 可以记录上次查询的最大ID，下次查询时直接根据该ID来查询

##### 优化 union 查询
使用 union all 替代 union 查询，效率更高，数据去重放到应用层面
