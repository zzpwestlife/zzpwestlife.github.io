---
title: 文件上传插件 dropzone 接入方法
date: 2017-11-18 20:52:10
tags: dropzone
categories: html
---

[dropzone](http://www.dropzonejs.com/) 是一个简洁的文件上传插件，接入方便。

## 1. 首先下载 dropzone 插件的 css 和 js 文件， 放到 public 目录下。

## 2. 模板文件中引入 css 和 js

<!-- more -->

```
@section('add_css')
    {!! Html::style('/dropzone/dist/min/dropzone.min.css') !!}
@endsection
```

```
@section('add_script')
    <script src="/dropzone/dist/min/dropzone.min.js"></script>
@endsection
```

## 3. 采用渐进式的引入方式 html 中

```
<div class="dropzone dz-clickable" id="myDrop">
    <div class="dz-default dz-message" data-dz-message="">
        <span style="font-size: large">点击此处或拖拽文件到此处</span>
    </div>
</div>

```

## 4. js 文件中

```
@section('script')

    <script type="text/javascript">
        Dropzone.autoDiscover = false; // 这一行一定要放在ready之前
        $(document).ready(function () {
            var fileMaxSize = 20; // MB

            var myDropzone = new Dropzone("div#myDrop", {
                url: "/admin/files/upload",
                paramName: "file", // The name that will be used to transfer the file
                maxFilesize: fileMaxSize, // MB
                maxFiles: 1, // 一个一个来
		// 需要在headers中添加csrf
                headers: {
                    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                }
            });

            myDropzone.on('addedfile', function (file) {
                if (file.size > 1024 * 1024 * fileMaxSize) {
                    alert('文件大小不能超过 ' + fileMaxSize + 'M');
                    myDropzone.removeFile(file);
                }
                file.previewElement.addEventListener("click", function () {
//                    var filename = $(this).find('.dz-filename')[0].innerText;
//                    console.log(file);
                    var path = $('#path').val();
                    var filename = $('#filename').val();
                    if (window.confirm('你确定要删除 ' + file.name + ' 吗？')) {
                        $.ajax({
                            type: "GET",
                            url: "/admin/files/delete",
                            data: {path: path},
                            dataType: "JSON",
                            success: function (data) {
//                                console.log(data);
                                if (0 == data.error) {
                                    myDropzone.removeFile(file);
                                } else {
                                    alert('删除失败，稍后重试');
                                }
                            }
                        });
                    } else {
                        return false;
                    }
                });
            });

            myDropzone.on('success', function (file, response) {
//                console.log(file);
//                console.log(response);
                if (response.errno == 0) {
//                    alert('文件上传成功');
                    $('#filename').attr('value', response.data['original_filename']);
                    $('#path').attr('value', response.data['path']);
                    $('#uri').attr('value', response.data['uri']);
                    $('#hash').attr('value', response.data['file_hash']);
                } else {
                    alert(response.msg);
                    myDropzone.removeFile(file);
                }
            });

            myDropzone.on('error', function (file, errorMessage) {
//                console.log(errorMessage);
            });
            myDropzone.on('removefile', function (file) {
                alert('remove file');
            });
        });
    </script>
@endsection
```

## 5. layout 文件中 ，在head 标签内需加入 csrf_token


```
<meta name="csrf-token" content="{{ csrf_token() }}">
```


## 6. php 文件，异步处理上传的文件

```
public function upload(Request $request)
{
    $returnData = fileUpload($request->file('file'), 'file');
    return response()->json($returnData)->setCallback($request->input('callback'));
}
```

```
// 文件上传方法
function fileUpload($file, $dirName)
{
    $returnData = [
        'errno' => -1,
        'msg' => '',
        'data' => ''
    ];

    $path = sprintf('%s/uploads/%s/%s/', DATA_ROOT, $dirName, \Carbon\Carbon::now()->month);
    autoMakeDir($path);

//        var_dump($file->getError()); // 0
//        var_dump($file->getFilename()); // php3R7aUM
//        var_dump($file->getExtension());
//        var_dump($file->getClientMimeType()); // image/png
//        var_dump($file->getClientOriginalExtension()); // png
//        var_dump($file->getClientOriginalName()); // bef3df8aly1fbx05q2ra1j20b40b4mxi.png
//        var_dump($file->getErrorMessage()); // The file "bef3df8aly1fbx05q2ra1j20b40b4mxi.png" was not uploaded due to an unknown error.
//        var_dump($file->getBasename()); // php3R7aUM
//        var_dump($file->getPath()); // /tmp
//        var_dump($file->getPathname()); // /tmp/php3R7aUM
//        var_dump($file->getType()); // file
//        var_dump($file->getRealPath()); // /tmp/php3R7aUM

    $error = $file->getError();
    if ($error != 0) {
        $returnData['msg'] = $file->getErrorMessage();
    } else {
        $fileExt = $file->getClientOriginalExtension();
        // 新文件名
        $originalFilename = $file->getClientOriginalName();
        $realFilename = rtrim($originalFilename, '.' . $fileExt);
        $newFilename = sprintf('%s_%s_%s.%s', $realFilename, time(), rand(10000, 99999), $fileExt);
        $fileHash = hash_file('md5', $file->getPathname());
        // 不允许重复上传相同的文件
        $fileExist = \App\File::where('hash', $fileHash)->count();
        if ($fileExist) {
            $returnData['msg'] = '该文件已存在 请选择其他文件上传';
            $returnData['errno'] = 123;
        } else {
            // 移动文件
            $file->move($path, $newFilename);
            $returnData['errno'] = 0;
            $fileUri = sprintf('/uploads/%s/%s/', $dirName, \Carbon\Carbon::now()->month) . $newFilename;
            $returnData['data'] = [
                'path' => $path . $newFilename,
                'uri' => $fileUri,
                'filename' => $newFilename,
                'original_filename' => $originalFilename,
                'file_hash' => $fileHash,
            ];
        }
    }

    return $returnData;
}

```

```
// 自动创建目录方法
function autoMakeDir($filePath)
{
    if (!file_exists($filePath)) {
        mkdir($filePath, 0777, true);
    }
}
```