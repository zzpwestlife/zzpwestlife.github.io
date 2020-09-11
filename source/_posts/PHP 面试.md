### 1. echo(),print(),print_r()的区别？
- echo 和 print 不是一个函数，是一个语言结构；
- `print(string $arg)` 只有一个参数；
- `echo arg1,arg2` 可以输出多个参数，返回 `void` ；
- `echo` 和 `print` 只能打印出string，不能打印出结构；
- `print_r` 能打印出结构。比如:

<!--more-->

```
$arr = array("key"=>"value");
print_r($arr);

Array
(
    [key] => value
)
```

### 2. 语句include和require的区别是什么?
在失败的时候：
include 产生一个 warning ，而 require 直接产生错误中断；

require 在运行前载入；

include 在运行时载入；

require_once 和 include_once 可以避免重复包含同一文件。

### 3. php中传值与传引用有啥区别?
&表示传引用；

函数中参数传引用会将参数进行改变；

一般在输出参数有多个的时候可以考虑使用引用。

### 4. 下面哪项没有将john添加到users数组中？
```
(a) $users[] = 'john';
(b) array_add($users,'john');
(c) array_push($users,'john');
(d) $users ||= 'john';
```

答案为bd，php 里面无 `array_add` 函数，d项为语法错误的表达。

### 5. HTTP协议中几个状态码的含义。
- 200 : 请求成功，请求的数据随之返回 OK。
- 301 : 永久性重定向 Moved permanently。
- 302 : 暂时行重定向 Moved Temporarily
- 401 : 当前请求需要用户验证 Unauthorized。
- 403 : 服务器拒绝执行请求，即没有权限 Forbidden。
- 404 : 请求失败，请求的数据在服务器上未发现 Not found。
- 500 : 服务器错误。一般服务器端程序执行错误 Internal server error。
- 503 : 服务器临时维护或过载。这个状态时临时性的 Service unavailable。
- 504：超时，Gateway timeout


### 6. 写出一些php魔术方法。
```
__construct() 实例化类时自动调用。
__destruct() 类对象使用结束时自动调用。
__set() 在给未定义的属性赋值的时候调用。
__get() 调用未定义的属性时候调用。
__isset() 使用isset()或empty()函数时候会调用。
__unset() 使用unset()时候会调用。
__sleep() 使用serialize序列化时候调用。
__wakeup() 使用unserialize反序列化的时候调用。
__call() 调用一个不存在的方法的时候调用。
__callStatic()调用一个不存在的静态方法是调用。
__toString() 把对象转换成字符串的时候会调用。比如 echo。
__invoke() 当尝试把对象当方法调用时调用。
__set_state() 当使用var_export()函数时候调用。接受一个数组参数。
__clone() 当使用clone复制一个对象时候调用。
```

### 7. MySQL存储引擎 MyISAM 和 InnoDB 的区别。
```
a. MyISAM类型不支持事务处理等高级处理，而InnoDB类型支持.
b. MyISAM类型的表强调的是性能，其执行速度比InnoDB类型更快.
c. InnoDB不支持FULLTEXT类型的索引.
d. InnoDB中不保存表的具体行数，也就是说，执行select count(*) from table时，InnoDB要扫描一遍整个表来计算有多少行，但是MyISAM只要简单的读出保存好的行数即可.
e. 对于AUTO_INCREMENT类型的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中，可以和其他字段一起建立联合索引。
f. DELETE FROM table时，InnoDB不会重新建立表，而是一行一行的删除。
g. LOAD TABLE FROM MASTER操作对InnoDB是不起作用的，解决方法是首先把InnoDB表改成MyISAM表，导入数据后再改成InnoDB表，但是对于使用的额外的InnoDB特性(例如外键)的表不适用.
h. MyISAM支持表锁，InnoDB支持行锁。
```

### 8. 说出一些MySQL优化方法？
```
a. 设计良好的数据库结构，允许部分数据冗余，尽量避免join查询，提高效率。
b. 选择合适的表字段数据类型和存储引擎，适当的添加索引。
c. mysql库主从读写分离。
d. 找规律分表，减少单表中的数据量提高查询速度。
e. 添加缓存机制，比如memcached，apc等。
f. 不经常改动的页面，生成静态页面。
g. 书写高效率的SQL。比如 SELECT * FROM TABEL 改为 SELECT field_1, field_2, field_3 FROM TABLE.
```

### 9. 下面$a的结果是：
```
<?php
$a = in_array('01', array('1')) == var_dump('01' == 1);
?>
A true
B false

var_dump('01' == 1) // return NULL
in_array('01', array('1')) // return true

$a // false
```

答案为B

### 10. 说下php中empty()和isset()的区别。
```
isset 用于检测变量是否被设置，使用 isset() 测试一个被设置成 NULL 的变量，将返回 FALSE 。
empty 如果 var 是非空或非零的值，则 empty() 返回 FALSE。换句话说，""、0、"0"、NULL、FALSE、array()、var $var; 以及没有任何属性的对象（只有这8个，记住了）都将被认为是空的，如果 var 为空，则返回 TRUE 。

如果变量为 0 ，则empty()会返回TRUE，isset()会返回TRUE；
如果变量为空字符串，则empty()会返回TRUE，isset()会返回TRUE；
如果变量未定义，则empty()会返回TRUE，isset()会返回FLASE。

注意：isset() 只能用于变量，因为传递任何其它参数都将造成解析错误。若想检测常量是否已设置，可使用 defined() 函数。
当要 判断一个变量是否已经声明的时候 可以使用 isset 函数；
当要 判断一个变量是否已经赋予数据且不为空 可以用 empty函数；
当要 判断 一个变量 存在且不为空 先 isset 函数 再用 empty 函数；
```

### 题2：nginx使用哪种网络协议?

答案：nginx是应用层 我觉得从下往上的话 传输层用的是tcp/ip 应用层用的是http fastcgi负责调度进程。

### cookie 和session 的区别：

1. cookie数据存放在客户的浏览器上，session数据放在服务器上。

2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗
   考虑到安全应当使用session。

1. session 会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能
   考虑到减轻服务器性能方面，应当使用COOKIE。

4. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。

1. 所以个人建议：
   将登陆信息等重要信息存放为SESSION，其他信息如果需要保留，可以放在COOKIE中
   
   ### 题9：数据库中的事务是什么?

答案：事务（transaction）是作为一个单元的一组有序的数据库操作。如果组中的所有操作都成功，则认为事务成功，即使只有一个操作失败，事务也不成功。如果所有操作完成，事务则提交，其修改将作用于所有其他数据库进程。如果一个操作失败，则事务将回滚，该事务所有操作的影响都将取消。

### 题11：简述下面程序的输出结果, 简要说明为什么, 如何解决这类问题? 
```php
<?php 
$tmp = 0 == "a"? 1: 2; 
echo $tmp; 
?> 
```
答案： 1

int和string类型强制转换造成的，0 ＝＝ 0 肯定是true啊 

PHP是弱类型
```
$tmp = 0 === "a"? 1: 2; 
echo $tmp; 
```
这样就是2 

### 题12：什么是MVC？

答案：MVC由Model（模型）, View（视图）和Controller（控制器）组成，PHP MVC可以更高效地管理好3个不同层的PHP代码。

- Model：数据信息存取层。
- View：view层负责将应用的数据以特定的方式展现在界面上。
- Controller：通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据。


### 
题21：简述两种屏蔽php程序的notice警告的方法 

答案：

1. 在程序中添加：error_reporting (E_ALL & ~E_NOTICE); 
2. 修改php.ini中的：error_reporting = E_ALL 改为：error_reporting = E_ALL & ~E_NOTICE 
3. error_reporting(0);或者修改php.inidisplay_errors=Off 

### 题28：写一个排序算法，可以是冒泡排序或者是快速排序，假设待排序对象是一个维数组

答案：

冒泡排序（数组排序）：原理：两两相邻的数进行比较，如果反序就交换，否则不交换
``` php
function bubble_sort($array)
{
    $count = count($array);
    if ($count <= 0) return false;
    for($i=0; $i<$count; $i++){
        for($j=$count-1; $j>$i; $j--){
            if ($array[$j] < $array[$j-1]){
                $tmp = $array[$j];
                $array[$j] = $array[$j-1];
                $array[$j-1] = $tmp;
            }
        }
    }
    return $array;
}
```

快速排序（数组排序）：

原理：找到当前数组中的任意一个元素（一般选择第一个元素），作为标准，新建两个空数组，遍历整个数组元素，
如果遍历到的元素比当前的元素要小，那么就放到左边的数组，否则放到右面的数组，然后再对新数组进行同样的操作

不难发现，这里符合递归的原理，所以我们可以用递归来实现。

使用递归，则需要找到递归点和递归出口：
- 递归点：如果数组的元素大于 1，就需要再进行分解，所以我们的递归点就是新构造的数组元素个数大于 1
- 递归出口：我们什么时候不需要再对新数组不进行排序了呢？就是当数组元素个数变成 1 的时候，所以这就是我们的出口。

``` php
function quicksort($array) {
    if (count($array) <= 1) return $array;
    $key = $array[0];
    $left_arr = array();
    $right_arr = array();
    for ($i=1; $i<count($array); $i++){
        if ($array[$i] <= $key)
            $left_arr[] = $array[$i];
        else
            $right_arr[] = $array[$i];
    }
    $left_arr = quicksort($left_arr);
    $right_arr = quicksort($right_arr);
    return array_merge($left_arr, array($key), $right_arr);
}
```
