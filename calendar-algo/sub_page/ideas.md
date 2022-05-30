<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/may_first.png" style="width:65%" class="center-screen" />

<v-click>

```php
$board = [
  0, 0, 0, 0, 1, 0, 1
  0, 0, 0, 0, 0, 0, 1
  1, 0, 0, 0, 0, 0, 0
  0, 0, 0, 0, 0, 0, 0
  0, 0, 0, 0, 0, 0, 0
  0, 0, 0, 0, 0, 0, 0
  0, 0, 0, 1, 1, 1, 1
];
```

</v-click>

<style>
    .shiki-container {
        position: absolute;
        top: 25%;
        left: 56%;
        margin-right: -40%;
        transform: translate(-50%, -50%);
    }
</style>

<arrow v-click x1="650" y1="170" x2="580" y2="90" color="#f00" width="1" arrowSize="1" />
<arrow v-click x1="545" y1="150" x2="495" y2="125" color="#f00" width="1" arrowSize="1" />

---

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/family.png" style="width:75%" class="center-screen" />

<p v-click class="absolute top-60 left-65 transform" style="font-size:100px;color:deeppink;">46!</p>

<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/family2.png" style="width:75%" class="center-screen" />

---

```mermaid {theme: 'forest', scale: 0.5}
graph TD
board[空 board] --> rec1[rec1]
board -->rec2[rec2]
board -->recs[...]
board -->rec36[rec36]

rec1 --> sf1[short_flash1]
rec1 --> sf2[short_flash2]
rec1 --> sfs[...]

rec2 --> sf3[short_flash1]
rec2 --> sf4[short_flash2]
rec2 --> sfs2[...]

rec36 --> sf5[short_flash1]
rec36 --> sf6[short_flash2]
rec36 --> sfs3[...]

sf1 --> sl1[short_l1]
sf1 --> sl2[short_l2]
sf1 --> sls[...]

sl2 --> ao1[ao1]
sl2 --> ao2[ao2]
sl2 --> aos[...]

ao2 --> lf1[long_flash1]
ao2 --> lfs[...]
```

---
layout: section
---

<img v-after src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/learn.png" style="height:20%;position:absolute;top:45%;left:7%" class="rounded shadow" />

::right::

- 递归和迭代
- 树和图的遍历 BFS & DFS
- 回溯和剪枝
- 贪心算法
- 岛屿问题

---
layout: fact
---

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/what_is_recursion.png" style="height:20%;position:absolute;top:10%;left:10%" class="rounded shadow" />
<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/stairs.png" style="width:50%;position:absolute;top:35%;left:10%" class="rounded shadow" />
<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/canon.png" style="height:20%;position:absolute;top:10%;left:40%" class="rounded shadow" />
<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/inception.jpeg" style="height:74.6%;position:absolute;top:10%;left:65%" class="rounded shadow" />

---
layout: default
---

# 递归 Recursion

- 你想知道递归是什么，你得先知道什么是递归.
- To understand recursion, you must understand recursion.
- 从前有座山，山上有座庙，庙里有个老和尚在给小和尚讲故事："从前有座山，山上有座庙，庙里有个老和尚在给小和尚讲故事：……"
- Bing = Bing is not google (Bing 不是谷歌)
- GNU = GNU's Not Unix (GNU 不是 Unix)
- PHP: Hypertext Preprocessor

<h2 v-click>这些都不是真正意义上的递归 !!!</h2>

---
layout: default
---

# 递归三要素

<br>

1. 确定递归函数的参数和返回值
2. 确定递归终止条件, 即函数 return 的出口
3. 确定单层递归的逻辑: 明确每次递归要进行什么操作

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/recursion.gif" style="height:50%;position:absolute;top:20%;left:55%" class="rounded shadow" />

---
layout: default
---

# 递归: 1+2+3+...+100

## 迭代法

```php
$sum = 0;
for ($i = 1; $i <= 100; $i++) {
    $sum += $i;
}

return $sum;
```

## 递归法

```php {all|1,2|3-6|8,9|all}
// 1. 方法定义, 入参和出参
function sum(int $n): int {
    // 2. 递归终止条件
    if ($n === 1) {
        return 1;
    }

    // 3. 单层递归逻辑
    return sum($n - 1) + $n;
}
```

---
layout: default
---

# 递归: n 的阶乘

<br>
<br>

```php {all|1,2|3-6|8-9|all}
// 1. 方法定义, 入参和出参
function factorial(int $n): int {
    // 2. 递归终止条件
    if ($n === 1) {
        return $n;
    }

    // 3. 单层递归逻辑
    return $n * factorial($n - 1);
}
```

---

# 递归: 斐波那契数列的第 n 项

<br>
<br>

```php {all|1,2|3-6|8-9|all}
// 1. 方法定义, 入参和出参
function fib(int $n): int {
    // 2. 递归终止条件
    if ($n <= 2) {
        return 1;
    }

    // 3. 单层递归逻辑
    return fib($n - 1) + fib($n - 2);
}
```
