---
title: 富文本编辑器 wangEditor 接入方法
date: 2017-11-18 20:51:55
tags: wangEditor
categories: html
---

[wangEditor](https://www.kancloud.cn/wangfupeng/wangeditor3/332599) 是基于javascript和css开发的 Web富文本编辑器， 轻量、简洁、易用、开源免费


## 1. 下载所需的 css 和 js 文件，放到 public 目录下

## 2. 模板文件中引入 css 和 js

其中， js 文件必须放到 jquery 后

<!-- more -->

```
<link rel="stylesheet" href="/bower_components/wangEditor/release/wangEditor.min.css">

<script type="text/javascript" src="/bower_components/wangEditor/release/wangEditor.min.js"></script>
```

## 3. 在 html 中，添加一个需要展示编辑器的 div
```
<div id="editor">
    <p>
        @if(!empty($post)){!! $post->content !!}@endif
    </p>
</div>
```

## 4. js 文件中
```
@section('script')
    <script type="text/javascript">
        $(document).ready(function () {

            var E = window.wangEditor;
            var editor = new E('#editor');
            editor.customConfig.uploadImgServer = '/admin/posts/image/upload';  // 上传图片到服务器
            editor.customConfig.uploadImgHeaders = {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            };
            editor.customConfig.uploadFileName = 'wangEditorImg';
            // 将图片大小限制为 1M
            editor.customConfig.uploadImgMaxSize = 1024 * 1024;
            // 限制一次最多上传 5 张图片
            editor.customConfig.uploadImgMaxLength = 5;
            // 隐藏“网络图片”tab
            editor.customConfig.showLinkImg = false;
            editor.customConfig.zIndex = 1;
            editor.customConfig.menus = [
                'head',  // 标题
                'bold',  // 粗体
                'italic',  // 斜体
                'underline',  // 下划线
                'strikeThrough',  // 删除线
                'foreColor',  // 文字颜色
                'backColor',  // 背景颜色
                'link',  // 插入链接
                'list',  // 列表
                'justify',  // 对齐方式
                'quote',  // 引用
                'emoticon',  // 表情
                'image',  // 插入图片
                'table',  // 表格
//                'video',  // 插入视频
                'code',  // 插入代码
                'undo',  // 撤销
                'redo'  // 重复
            ];
            editor.create();
	</script>
@endsection

```

## 5. PHP 文件异步处理上传的图片

需要提前配置文件路径，服务器中单独设置一个目录，最好在项目目录之外，可以使多个项目都同时能访问到，同时配置一个二级域名或端口直接指向该目录。

```
public function imageUpload(Request $request)
{
    $returnData = imageUpload($request->file('wangEditorImg'), 'posts', true);
    return response()->json($returnData)->setCallback($request->input('callback'));
}
```

```
// 处理文件上传，需要返回图片路径数组
function imageUpload($file, $dirName, $isWangEditor = false)
{
    $returnData = [
        'errno' => -1,
        'msg' => '',
        'data' => ''
    ];

    $path = sprintf('%s/uploads/%s/%s/', DATA_ROOT, $dirName, \Carbon\Carbon::now()->month);
    autoMakeDir($path);

    $error = $file->getError();
    if ($error != 0) {
        $returnData['msg'] = $file->getErrorMessage();
    } else {
        $fileExt = $file->getClientOriginalExtension();
        // 新文件名
        $newFilename = sprintf('%s_%s.%s', time(), rand(10000, 99999), $fileExt);
        // 移动文件
        $file->move($path, $newFilename);
        $returnData['errno'] = 0;
        $fileUrl = sprintf('%s/uploads/%s/%s/', DATA_URL, $dirName, \Carbon\Carbon::now()->month) . $newFilename;
        if ($isWangEditor) {
            $returnData['data'] = [$fileUrl];
        } else {
            $returnData['data'] = $newFilename;
        }
    }

    return $returnData;
}
```