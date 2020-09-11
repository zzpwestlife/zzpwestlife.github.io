---
title: Laravel 后台搜索功能的实现
date: 2017-12-22 22：28：12
tags: Laravel
categories: laravel
---

目标:
1. 实现根据多个条件的搜索功能，在结果页展示搜索结果
2. 不同的搜索条件，匹配方式不同，如时间为时间段，名字为模糊搜索等
3. 搜索结果也要分页，并且所有页展示的都是搜索结果
4. 搜索条件保留在搜索框中
5. 一键清空所有搜索条件

<!-- more -->

## 1. list 的 blade 模板页面

### 1. 在列表之前，添加搜索框选项的html代码

```
<div class="row" id="frm_search_info" style="margin-bottom: 30px">
    <div class="col-sm-12">
        <div class="col-sm-4">
            用户：
            <select id="user_id" name="user_id" class="form-control select2">
                <option value="0">请选择</option>
                @foreach($users as $key => $value)
                    <option value="{{$value->id}}" @if($value->id == $searchParams['user_id']) selected
                            @endif>{{$value->name}}</option>
                @endforeach
            </select>
        </div>
        <div class="col-sm-4">
            论坛：
            <select id="forum_id" name="forum_id" class="form-control select2">
                <option value="0">请选择</option>
                @foreach($forums as $key => $value)
                    <option value="{{$value->id}}"
                            @if($value->id == $searchParams['forum_id']) selected
                            @endif>{{$value->name}}</option>
                @endforeach
            </select>
        </div>
        <div class="col-sm-4">
            修改日期：<input type='text' class="form-control" id="start_time" name="start_time"
                        value="{{$searchParams['startTime']}}"
                        placeholder="开始时间"/>

            - <input type='text' class="form-control" id="end_time" name="end_time"
                     value="{{$searchParams['endTime']}}"
                     placeholder="结束时间"/>
        </div>
        <div class="col-sm-4">
            <input type="text" name="name" id="" class="form-control" placeholder="文件名"
                   value="{{$searchParams['name']}}">
            <button name="" id="btn_clear" class="btn btn-default" type="submit">
                清空
            </button>
            <button name="" id="btn_search" class="btn btn-success" type="submit">
                搜索
            </button>
        </div>
    </div>
</div>
```

### 2. 同时添加处理选项的js代码，用于拼接跳转url

```
$(document).ready(function () {
    $(".select2").select2({language: "zh-CN"});
    // 搜索
    searchWithParams('/files/?page=1');
});
```

### 3. js 方法在单独的文件中， blade 模板中引入

```
/**
 * 搜索按钮
 * @param url
 */
function searchWithParams(url) {
    $("#btn_search").click(function () {
        var params = {};
        $("#frm_search_info select, #frm_search_info input[type=text], #frm_search_info input[type=hidden]").each(function () {
            var value = $(this).val();
            if ('' != value) {
                params[$(this).attr('name')] = value;
            }
        });

        if (!$.isEmptyObject(params)) {
            url += "&" + parserParams2Url(params);
        }
        location.href = url;
    });
}

parserParams2Url = function (params) {
    var url = '';
    $.each(params, function (index, item) {
        if (url) {
            url += '&';
        }
        url += index + '=' + item;
    });
    return url;
};
// 清空搜索条件
$("#btn_clear").click(function () {
    $('#frm_search_info').find('select').each(function () {
        $(this).find('option:first').prop("selected", true);
        $(this).change();
    });
    $("#frm_search_info input[type=text]").each(function () {
        $(this).val("");
    });
    $("#frm_search_info input[type=number]").each(function () {
        $(this).val("");
    });
});
```

### 4. 同时，分页部分也要带上搜索参数 appends

```
<div class="row">
    <div class="col-sm-12">
        {{$files->appends($searchParams)->links()}}
    </div>
</div>
```

## 2. 控制器

### 1. 首先接收所有的请求参数，包括搜索参数

```
$userId = intval($request->input('user_id', 0));
$forumId = intval($request->input('forum_id', 0));
$startTime = trim($request->input('start_time', ''));
$endTime = trim($request->input('end_time', ''));
$name = trim($request->input('name', ''));
```

### 2. 拼接搜索的query

```
$query = File::with('forum')->with('user')->orderBy('updated_at', 'desc');
if (!empty($name)) {
        $query->where('filename', 'like', '%' . $name . '%');
}
if (!empty($startTime) && !empty($endTime) && ($endTime > $startTime)) {
    $query->where('updated_at', '>', $startTime);
    $query->where('updated_at', '<', $endTime);
}
if (!empty($userId)) {
    $query->where('user_id', $userId);
}
if (!empty($forumId)) {
    $query->where('forum_id', $forumId);
}
$query = $query->paginate();
```

### 3. 如果搜索query中包含多个orWhere条件，并且需要以括号的形式处理时，需要使用闭包

```
if (!empty($name)) {
    $query->where(function ($query) use ($name) {
        $query->where('name', 'like', '%' . $name . '%')
            ->orWhere('alias', 'like', '%' . $name . '%')
            ->orWhere('alias_abbr', 'like', '%' . $name . '%');
    });
}
```

### 4. 最后，给模板文件返回所需参数，搜索条件以数组的形式返回，便于appends到分页语句中

```
$returnData = [
    'files' => $query,
    'searchParams' => [
        'startTime' => $startTime,
        'endTime' => $endTime,
        'name' => $name,
        'user_id' => $userId,
        'forum_id' => $forumId,
    ],
    'forums' => $forums,
    'users' => $users
];
return view('/file/index', $returnData);
```
