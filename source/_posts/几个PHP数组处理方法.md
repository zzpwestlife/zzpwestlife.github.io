---
title: 几个PHP数组处理方法 & 几个常用的其他方法
date: 2017-12-05 16:45:37
tags: PHP
categories: PHP
---

记录几个常用的处理PHP数组的方法，方便查用。
1. 二维数组按某一字段去重
2. 二维数组按多个字段去重
2. 二维数组按某一字段排序
3. 二维数组按多个字段排序

<!-- more -->

## 1. 二维数组按某一字段去重

```
/**
 * @comment 二维数组按某一字段去重
 * @param array $arr
 * @param string $field
 * @return array
 * @author zzp
 * @date 2017-12-05
 */
function getUniqueFromTwoDimensionalArray($arr, $field)
{
    $result = [];
    foreach ($arr as $item) {
        if (is_array($item)) {
            $rKey = $item[$field];
        } else {
            $rKey = $item->$field;
        }
        $result[$rKey] = $item;
    }

    return array_values($result);
}

```


## 2. 二维数组按多个字段去重

```
/**
 * @comment 二维数组/对象按一个/多个字段去重
 * @param array|object $arr
 * @param string|array $filed
 * @return array
 * @author zzp
 * @date 2017-12-14
 */
function getUniqueArr($arr, $filed)
{
    $result = [];
    if (is_string($filed)) {
        $filed = [$filed];
    }

    foreach ($arr as $item) {
        if (is_array($item)) {
            $rKey = '';
            foreach ($filed as $key) {
                $rKey .= md5($item[$key]);
            }
        } else {
            // 对象
            $rKey = '';
            foreach ($filed as $key) {
                $rKey .= md5($item->$key);
            }
        }
        $result[$rKey] = $item;
    }

    return array_values($result);
}
```

## 3. 二维数组按某一字段排序

```
/**
 * @comment 二维数组排序
 * 用法：sortTwoDimensionalArray($arr, $filed, $direction = 'SORT_DESC')
 * @param array $arr
 * @param string $filed 排序字段
 * @param string $direction 排序顺序标志 SORT_DESC 降序；SORT_ASC 升序
 * @author zzp
 * @date 2017-12-05
 */
function sortTwoDimensionalArray($arr, $filed, $direction = 'SORT_DESC')
{
    $args = func_get_args();
    $arrSort = [];
    foreach ($arr AS $uniqid => $row) {
        foreach ($row AS $key => $value) {
            $arrSort[$key][$uniqid] = $value;
        }
    }
    if ($direction) {
        array_multisort($arrSort[$filed], constant($direction), $arr);
    }

    return $arr;
}
```

## 4. 二维数组按多个字段排序

```
/**
 * @comment 二维数组根据多个字段排序，类似mysql中的多个 order by
 * 用法： sortArrByManyField($arr, 'age', SORT_DESC, 'id', SORT_ASC, 'id', SORT_DESC)
 * @return mixed|null
 * @throws Exception
 * @author zzp
 * @date 2017-12-05
 */
function sortArrByManyField()
{
    $args = func_get_args();
    if (empty($args)) {
        return null;
    }
    $arr = array_shift($args);
    if (!is_array($arr)) {
        throw new Exception("第一个参数不为数组");
    }

    foreach ($args as $key => $field) {
        if (is_string($field)) {
            $temp = [];
            foreach ($arr as $index => $val) {
                $temp[$index] = $val[$field];
            }
            $args[$key] = $temp;
        }
    }
    $args[] = &$arr; // 引用值
    call_user_func_array('array_multisort', $args);
    return array_pop($args);
}
```


## 5. 如果路径不存在，自动创建

```
/**
 * 如果路径不存在，自动创建
 * @param $filePath
 * User: zzp
 * Date: 2017-03-13
 * @return bool
 */
function autoMakeDir($filePath)
{
    if (!file_exists($filePath)) {
        mkdir($filePath, 0777, true);
    }
}

```

## 6. 自动删除文件 

```
function autoUnlink($file)
{
    if (file_exists($file)) {
        unlink($file);
    }
}

```


## 7. 获取IP地址

由于服务器nginx代理的缘故 先通过 header 拿到真实ip
```
function getClientIp()
{
    if (isset($_SERVER['HTTP_X_REAL_IP'])) {
        return $_SERVER['HTTP_X_REAL_IP'];
    } elseif (isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
        return $_SERVER['HTTP_X_FORWARDED_FOR'];
    }

    return request()->getClientIp();
}

```

## 8. 递归显示当前指定目录下所有文件

```
/**
 * 使用dir函数
 * @param string $dir 目录地址
 * @param boolean $recursion 是否递归
 * @return array $files 文件列表
 */
function getFiles($dir, $recursion = false)
{
    $files = array();

    if (!is_dir($dir)) {
        return $files;
    }

    $d = dir($dir);
    while (false !== ($file = $d->read())) {
        if ($file != '.' && $file != '..') {
            $filename = $dir . "/" . $file;

            if (is_file($filename)) {
                $files[] = $filename;
            } else {
                if ($recursion) {
                    $files = array_merge($files, getFiles($filename, $recursion));
                }
            }
        }
    }
    $d->close();
    return $files;
}

```


## 9. 文章的分享内容

```
function getShareContent($str, $length = 0)
{
    // 把 video 标签中的 "你的浏览器不支持Video标签" 文字去掉
    $pattern = '/<video[\s\S]*<\/video>/';
    $str = preg_replace($pattern, '', $str);
    $str = html_entity_decode(strip_tags($str));
    $str = str_replace(PHP_EOL, '', $str);
    $strArray = array(" ", "　", "\t", "\n", "\r", "&hellip;", "&mdash;");
    $str = str_replace($strArray, '', $str);
    $str = str_replace("'", '"', $str);
    $str = trim(str_replace(chr(194) . chr(160), '', $str)); //shungao20171124 修复UFT8转码特殊字符问题
    if (empty($length) && mb_strlen($str) > 120) {
        $str = mb_substr($str, 0, 120, 'utf-8') . '...';
    }
    if (empty($str)) {
        $str = '来自...';
    }
    return $str;
}

```

## 10. 是否为微信内

```
function isWechat()
{
    $ua = isset($_SERVER['HTTP_USER_AGENT']) ? $_SERVER['HTTP_USER_AGENT'] : "";
    return true == strpos($ua, 'MicroMessenger');
}

```


## 11. mm

```
if (!function_exists('mm')) {
    /**
     * Dump the passed variables and end the script.
     *
     * @param  mixed
     * @return void
     */
    function mm()
    {
        array_map(function ($x) {
            (new \Illuminate\Support\Debug\Dumper)->dump($x);
        }, func_get_args());
    }
}

```

## 12. is_empty

```
if (!function_exists('is_empty')) {
    function is_empty($obj)
    {
        if (empty($obj)) {
            return true;
        }

        if ($obj instanceof Illuminate\Support\Collection) {
            $items = $obj->all();
            return empty($items);
        }
        return false;
    }
}

```

## 13. 字符串转16进制

```
/**
 * @comment 字符串转16进制
 * @param $string
 * @return string
 * @author zzp
 * @date 2017-12-12
 */
function strToHex($string)
{
    $hex = '';
    for ($i = 0; $i < strlen($string); $i++) {
        $ord = ord($string[$i]);
        $hexCode = dechex($ord);
        $hex .= substr('0' . $hexCode, -2);
    }
    return strToUpper($hex);
}
```

## 14. 判断两个时间段是否有重叠
```
// https://jjyy.de/post/judge-two-date-range-is-overlap.html
function timeIsOverlap($timeAStart, $timeAEnd, $timeBStart, $timeBEnd)
{
    return !($timeAEnd < $timeBStart || $timeBEnd < $timeAStart);
}
```

