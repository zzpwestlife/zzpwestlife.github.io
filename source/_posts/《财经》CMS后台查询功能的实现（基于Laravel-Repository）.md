---
title: 《财经》CMS后台查询功能的实现（基于Laravel Repository）
date: 2017-05-24 14:29:57
tags: laravel
categories: laravel
---

使用 [RequestCriteria](https://github.com/andersao/l5-repository) 统一搜索功能，可自动根据 url 按字段进行搜索，无需写方法

<!--more-->

# 第一部分 单表查询

## 1. 后台搜索框的基本代码：

```
<div class="panel-group">
    <div class="panel panel-default" style="border: none;">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a data-toggle="collapse" href="#wrapper_search"
                   aria-expanded="false" @if(false == $searchMode)class="collapsed" @endif >
                    搜索相关
                </a>
            </h4>
        </div>
        <div id="wrapper_search" class="panel-collapse collapse @if(true == $searchMode)in @endif"
             aria-expanded="false">
            <div class="panel-body">
                <div class="portlet-heading">
                    <form id="frm_search_info" class="text-dark form-horizontal" action="">
                        <input type="hidden" value=""/>
                        <div class="form-group col-sm-4 pull-left">
                            <label for="order_sn" class="control-label col-sm-4">订单号</label>

                            <div class="col-sm-8">
                                <input type="text" class="form-control" id="title" name="id"
                                       value="{{isset($searchCondition['id'])?$searchCondition['id']:''}}"/>

                            </div>
                        </div>

                        <div class="form-group col-sm-4 pull-left">
                            <label for="snap_goods_name" class="control-label col-sm-4">商品名称</label>

                            <div class="col-sm-8">
                                <input type="text" class="form-control" id="title" name="snap_goods_name"
                                       value="{{isset($searchCondition['snap_goods_name'])?$searchCondition['snap_goods_name']:''}}"/>

                            </div>
                        </div>

                        <div class="form-group col-sm-4 pull-left">
                            <label for="paymentInfo.payment_type" class="control-label col-sm-4">支付方式</label>

                            <div class="col-sm-8">
                                <select class="form-control input-lg select2" id="paymentInfo.payment_type" name="paymentInfo.payment_type"
                                        style="width: 192px;">
                                    <option value="">全部</option>
                                    @if(isset($paymentType))
                                        @foreach($paymentType as $index => $item)
                                            <option value="{{$index}}"
                                                    @if(isset($searchCondition['paymentInfo.payment_type']) && $index == $searchCondition['paymentInfo.payment_type']) selected="selected" @endif>{{$item}}</option>
                                        @endforeach
                                    @endif
                                </select>

                            </div>
                        </div>
                        <div class="form-group col-sm-4 pull-left">
                            <label for="created_at@" class="control-label col-sm-4">至</label>

                            <div class="col-sm-8">
                                <div class="input-group datetimepicker date" id="end_time_picker">
                                    <input class="form-control" id="created_at@"
                                           name="created_at@" type="text"
                                           value="@if(isset($searchCondition['created_at@']) && $searchCondition['created_at@']){{$searchCondition['created_at@']}}@endif">
                                    <span class="input-group-addon" for="created_at@">
                            <span class="glyphicon glyphicon-calendar"></span>
                        </span>
                                </div>

                            </div>
                        </div>

                        <div class="form-group col-sm-4 pull-right">
                            <button type="button" class="btn btn-default m-l-10" id="btn_clear">清空</button>
                            <button type="button" class="btn btn-purple m-l-10" id="btn_export">
                                Excel 导出
                            </button>
                            <button type="button" class="btn btn-primary m-l-10" id="btn_search">搜索</button>

                        </div>

                        <div class="clearfix"></div>

                    </form>
                </div>
            </div>
        </div>
    </div>

</div>
```
要求表单中的 name 与数据表中的字段名完全一致。
注意：这里是 button，而不是 submit


## 2. 在YourModelRepositoryEloquent.php (如FlashRepositoryEloquent.php) 中添加 $fieldSearchable 字段

如：

```
protected $fieldSearchable = [
    'id',
    'snap_goods_name' => 'like',
    'username',
    'order_status',
    'created_at' => ">=",
    'created_at@' => "<=",
    'source_id',
    'paymentInfo.payment_type'
];
```
注意：
- 要列出所有需要进行搜索的字段。
- 等于的可以省略，
- 模糊搜索用 like，
- 范围搜索也直接写。
- 如果一个字段需要用一次以上，需要对 key 进行重写，加一个@

其中，最后一个功能是在 BaseRepositoryEloquent 中定义的。

默认的键值间的分隔符为：， 如http://prettus.local/users?search=name:John Doe;email:john@gmail.com
可以进行自定义。
在 BaseRepositoryEloquent.php 的构造方法中，
`$this->keyValueSeparator = '|:'`。

组件中默认不包含范围选择符，即sql中的between用法。我们进行了改写，以支持该用法。

BaseRepositoryEloquent 中，添加解析搜索url的方法。

```
public function parserSearchData($searchData)
{
    if (!empty($searchData) && is_array($searchData)) {
        foreach ($searchData as $index => $item) {
            $method = sprintf("parser%s4Search", ucfirst(camel_case(str_replace('@', '', $index))));
            $model = $this->getModel();
            if (method_exists($model, $method)) {
                $searchData[$index] = $model->{$method}($item);
            }
        }
    }

    return $searchData;
}
```

以及解析原始搜索数据的方法

```
public function parserOriginSearchData($search)
{
    $searchData = [];
    if (stripos($search, $this->keyValueSeparator)) {
        $fields = explode($this->paramSeparator, $search);

        foreach ($fields as $row) {
            try {
                list($field, $value) = explode($this->keyValueSeparator, $row);
                $searchData[trim($field)] = trim($value);
            } catch (\Exception $e) {
                //Surround offset error
            }
        }
    }
    return $searchData;
}
```

业务需要，添加了整合后的获取搜索参数的方法

```
public function getSearchData($param = null, $is_origin = true)
{
    $search = $this->request->get(config('repository.criteria.params.search', 'search'), null);
    $searchData = $this->parserOriginSearchData($search);
    if (false == $is_origin) {
        $searchData = $this->parserSearchData($searchData);
    }
    if (!empty($param)) {
        if (isset($searchData[$param])) {
            $searchData = $searchData[$param];
        } else {
            $searchData = null;
        }
    }
    return $searchData;
}
```

## 3. js 文件中添加解析url的方法。

```
// 搜索 按钮
searchWithParams('/magazine/order?page=1');

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
            url += "&search=" + parserParams2Url(params);
        }

        location.href = url;
    });

}
```

该方法实现了对所有输入框、下拉框等搜索框内容的收集并处理，生成一个 Repository 可解析的 url。


## 4. 回显的数据不需要通过控制器获取后回传，在模板中通过 $searchCondition 全局变量获取即可

如何使用？
控制器中的方法会非常简洁：

```
public function getIndex(Request $request)
{
    $flashes = $this->repositoryInterface->paginate();

    return View::make("flash/index", array(
        "flashes" => $flashes,
        "flashTypes" => Flash::$flashTypes,
    ));
}
```

---

# 第二部分 跨表查询

上述搜索方法只是实现了单表搜索，如果搜索涉及到跨表该怎么处理？

场景：在模型Order的列表页进行搜索，但搜索的关键字是模型Payment的一个字段，模型Payment有一个指向模型Order的外键 order_id。

实现：

## 1. 在 app/Models/Order 中，建立与 Payment 模型的关联关系。

```
/**
 * 支付信息关联关系
 * @return \Illuminate\Database\Eloquent\Relations\HasOne
 */
public function paymentInfo()
{
    return $this->hasOne('App\Models\Payment', 'order_id', 'id');
}
```

## 2. Order 的模板文件（列表 resources/views/backend/order/index.blade.php）中，搜索相关部分的 name 和字段名都要加上关联关系的方法前缀。

```
<div class="form-group col-sm-4 pull-left">
    <label for="paymentInfo.payment_type" class="control-label col-sm-4">支付方式</label>

    <div class="col-sm-8">
        <select class="form-control input-lg select2" id="paymentInfo.payment_type" name="paymentInfo.payment_type"
                style="width: 192px;">
            <option value="">全部</option>
            @if(isset($paymentType))
                @foreach($paymentType as $index => $item)
                    <option value="{{$index}}"
                            @if(isset($searchCondition['paymentInfo.payment_type']) && $index == $searchCondition['paymentInfo.payment_type']) selected="selected" @endif>{{$item}}</option>
                @endforeach
            @endif
        </select>

    </div>
</div>
```

## 3. 在YourModelRepositoryEloquent.php (如FlashRepositoryEloquent.php) 中添加 $fieldSearchable 字段

如：

```
protected $fieldSearchable = [
    'paymentInfo.payment_type'
];

要与模板文件的name一致。

