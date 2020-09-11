---
title: Laravel 去重、分组取最大、按时间二次分组、评论盖楼等
date: 2017-07-15 21:02:05
tags: laravel
categories: laravel
---

最近公司项目中遇到的几个问题，记录一下

## 1. 去重

业务：从数据库中取出用户的阅读足迹，由于用户在一天之内可能多次阅读同一篇文章，数据表中对于同一天同一用户同一文章可能有多条记录，只是打开时间和关闭时间不同。取出用户阅读足迹的时间，需要对当天的阅读足迹去重。

<!--more-->

```
阅读足迹表
CREATE TABLE `reading_footprints` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `user_id` int(11) NOT NULL COMMENT 'acct.member.id',
  `udid` varchar(255) COLLATE utf8_unicode_ci NOT NULL COMMENT '设备ID iOS IDFA， Android IMEI',
  `source_id` int(11) DEFAULT NULL COMMENT '数据源ID',
  `column_id` int(11) DEFAULT NULL COMMENT '栏目ID',
  `object_id` int(11) NOT NULL COMMENT '目前只记录文章的阅读记录， article_id',
  `type` tinyint(4) NOT NULL DEFAULT '0' COMMENT '类型 0 文章',
  `open_time` int(11) NOT NULL COMMENT '文章打开时间',
  `close_time` int(11) NOT NULL COMMENT '文章关闭时间',
  `non_breaking_days` int(11) NOT NULL DEFAULT '1' COMMENT '连续阅读天数',
  `accumulated_reading_time` int(11) NOT NULL COMMENT '累计阅读时间，秒',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=36 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci COMMENT='阅读足迹表';
```

例如：需要显示的一个数据是用户今天阅读了几篇文章，原生sql 语句是这样的

```
select distinct object_id from reading_footprints where user_id=237475 and open_time between 1499356800 and 1499443200

// 或者
select object_id from reading_footprints where user_id=237475 and open_time between 1489356800 and 1499443200 group by object_id
```

其中，时间戳在[这里](https://unixtime.51240.com/)查询。项目中，使用 Carbon 类获取。

```
$todayStart = Carbon::today()->timestamp;
$todayEnd = Carbon::tomorrow()->timestamp;
```

但这样的sql 语句只能获取到其中一个字段，或者时聚合函数的结果。

Laravel 中 ORM 方式写法见下一个问题。


## 2. 分组取最大

还是上面的阅读足迹问题，其中每一条记录中都有累计阅读天数和累计阅读时间的字段，所以对于文章去重的时候需要取出每篇文章的最后一条，即按文章id分组后还要取出每个分组中累计阅读时间最大的一条。

实现方法有好几种，基本思路都是先按文章id分组并取出最大的记录id或累计阅读时间等有限个字段（分组或去重时不能取出无关字段），然后再进行自连接关联查询或子查询。

```
原生sql
select a.* from reading_footprints a inner join
(select object_id, max(accumulated_reading_time) accumulated_reading_time from reading_footprints group by object_id) b 
on a.object_id = b.object_id and a.accumulated_reading_time = b.accumulated_reading_time order by close_time desc
```

Laravel ORM 中，先分组，后内连接

```
$sub = DB::table('reading_footprints')
    ->select(DB::raw('max(accumulated_reading_time) accumulated_reading_time'), 'object_id')
    ->groupBy('object_id');

$readingFootprints = DB::table('reading_footprints as a')
    ->select('a.*')
    ->join(DB::raw("({$sub->toSql()}) as b"), function ($join) {
        $join->on('a.accumulated_reading_time', '=', 'b.accumulated_reading_time');
        $join->on('a.object_id', '=', 'b.object_id');
    })
    ->orderBy('close_time', 'desc')->get();
```

抽象到 BaseRepositoryEloquent.php 中

```
/**
 * 单表分组取最大或最小 的数据
 * @param string $group_field
 * @param array $where
 * @param string $operate
 * @param string $operate_field
 * @param null $columns
 * User: Howard
 * Date: 2017-07-07
 * @return Collection|null
 */
public function getDataByGroup($group_field, $where = [], $operate = 'max', $operate_field = 'id', $columns = null)
{

    $data = null;
    $ids = $this->getIdByGroup($group_field, $where, $operate, $operate_field);
    if (!empty($ids)) {
        $data = $this->findWhereIn($operate_field, $ids, $columns)->keyBy($group_field);
    }
    return $data;
}


/**
 * 单表分组取最大或最小 的ID
 * @param $group_field
 * @param array $where
 * @param string $operate
 * @param string $operate_field
 * User: Howard
 * Date: 2017-07-07
 * @return array
 */
public function getIdByGroup($group_field, $where = [], $operate = 'max', $operate_field = 'id')
{
    $ids = [];
    if (empty($where)) {
        $self = $this;
    } else {
        $self = $this->whereWithParams($where);
    }
    $selfModel = $self->model->groupBy($group_field);
    $select = sprintf('%s(`%s`) as `%s`', strtolower($operate), $operate_field, $operate_field);
    $selfModel = $selfModel->select(DB::raw($select));
    $result = $selfModel->get();

    $this->resetModel();
    $this->resetCriteria();
    $this->resetScope();

    if (!is_empty($result)) {
        $ids = array_pluck($result, $operate_field);
    }

    return $ids;
}
```


## 3. 按时间二次分组

阅读足迹显示规则：日期1：[文章1；文章2]；日期2：[文章3；]... 即数据取出后为一维数组，再按关闭日期包成二维数组。

```
foreach ($footprints as $item) {
    if (isset($returnFootprints[$item->close_date])) {
        array_push($returnFootprints[$item->close_date], $footprint);
    } else {
        $returnFootprints[$item->close_date] = [$footprint];
    }
}
```


## 4. 评论盖楼

首先取出所有符合条件的评论，形成一个一维数组，然后进行处理（慢慢理解）

```
/**
 * 转换评论数据
 * @param $comment_info
 * @param $recursion
 * User: Howard
 * Date: 2017-06-08
 * @return array|map
 */
public function convertData4List($comment_info, $recursion = false)
{
    if ($comment_info instanceof Comment) {
        if (!is_empty($comment_info)) {
            $sc = new \stdClass();
        } else {
            $sc = $this->dealSingle($comment_info, $recursion);
        }
    } else {
        if (!is_empty($comment_info)) {
            $sc = [];
        }
        foreach ($comment_info as $index => $item) {
            $sc[] = $this->dealSingle($item, $recursion);
        }
    }
    return $sc;
}

/**
 * 处理单个评论 递归
 * @param $comment
 * @param $recursion
 * User: Howard
 * Date: 2017-06-08
 * @return array
 */
protected function dealSingle($comment, $recursion = false)
{
    $commentData = [
        'id' => $comment->id,
        'user_id' => $comment->uid,

        'username' => empty($comment->uname) ? '财经网友' : $comment->uname,
        'avatar' => '',
        'article_id' => $comment->articleid,
        'content' => $comment->content,
        'create_time' => $comment->ctime,
        'star_number' => rand(100,1000),
        'parent' => new \stdClass(),
    ];
//        dd($commentData);
    if ($recursion && !empty($comment->parent)) {
        // 递归调用
        $commentData['parent'] = $this->dealSingle($comment->parent, $recursion);
    }
    return $commentData;
}
```


