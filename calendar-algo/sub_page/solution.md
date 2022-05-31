<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/family.png" style="width:90%;position:absolute;top:10%;left:5%" class="rounded shadow" />

---
layout: section
---

## 优化方向: 贪心

Greedy

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/greedy2.png" style="width:20%;position:absolute;top:60%;left:10%" class="rounded shadow" />

::right::

1. 分支少 -> 解空间小;
2. 动态调整放置顺序;
3. 预定义排序.

---
layout: section
---

## 优化方向: 剪枝

Pruning

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/pruning2.png" style="width:25%;position:absolute;top:60%;left:10%" class="rounded shadow" />

::right::

1. 忽略最后一行和最后一列;
2. 越界或者覆盖;
3. 孤岛且面积小于 5, 忽略;
4. 限定所有孤岛面积的可选值;
5. 不会重复放置.

---
layout: section
---
## 可行解 Solution

## 伪代码

::right::

```php {all|6|7|9|14-16|18|19-21|all}
初始化 board
初始化 8 块积木, 共有 46 种拼法
8 块积木按贪心/预定义顺序排序

$queue = new SplQueue();
辅助队列初始化, 第一块积木的 36 种拼法入队
$level = 1;
// BFS
while (队列不为空) {
    队列中所有元素依次出队
    foreach ($queueItems as $item) {
        取出下一块积木, 每一种变形都尝试在当前 board 放置
        foreach ($shapes as $shape) {
            if (无法放置 || 面积不合适) { // 剪枝
                continue;
            }
            放置, 并将新的 board 入队
            $level++;
            if ($level === 8) { // 找到一种可行解, 结束搜索
                return;
            }
        }
    }
}
```

---
layout: section
---

## 可行解 Solution

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

::right::

### 抽象出的积木

```php
// 预定义排序: 'rec', 'short_flash', 'short_l', 'ao', 
//       'long_flash', 't', 'long_l', 'less_rec'
return [
    // 长方形的
    'rec' => [
        [[1, 1, 1], [1, 1, 1]],
        [[1, 1], [1, 1], [1, 1]],
    ],
    // 短闪电形, 旋转对称
    'short_flash' => [
        [[1, 0, 0], [1, 1, 1], [0, 0, 1]],
        [[0, 0, 1], [1, 1, 1], [1, 0, 0]],
        [[1, 1, 0], [0, 1, 0], [0, 1, 1]],
        [[0, 1, 1], [0, 1, 0], [1, 1, 0]],
    ],
    // 短 L 形, 旋转对称
    ...
```

---
layout: section
---

## 可行解 Solution

## 判断一块积木是否可在当前坐标放置

```php
// 剪枝
if (minIsland($board) < 5) {
    return [];
}
```

::right::

```php
function dropOneShape($board, $oneShape, $x, $y): array
{
    foreach ($oneShape as $i => $row) {
        foreach ($row as $j => $grid) {
            // 当前 grid 值为 0, 不可能引起冲突, 直接忽略
            if ($grid === 0) {
                continue;
            }
            $newX = $x + $i;
            $newY = $y + $j;
            // 越界 || 重合
            if ($newX >= $this->maxCol 
                || $newY >= $this->maxRow 
                || $board[$newX][$newY] !== 0) {
                return [];
            }
            $board[$newX][$newY] = $grid;
        }
    }
    // 剪枝代码
    return $board;
}
```

---

### 可行解: 最小岛屿面积剪枝

```php {all|9-11|all}
function minIsland(array $board): int
{
    $min = 49;
    for ($i = 0; $i < 7; ++$i) {
        for ($j = 0; $j < 7; ++$j) {
            if ($board[$i][$j] === 0) {
                $area = dfs($board, $i, $j);
                $min = min($min, $area);
                if ($min < 5) {
                    return $min;
                }
            }
        }
    }

    return $min;
}
```

---

### 可行解: 岛屿面积值剪枝

```php {all|1-4|all}
$available = [5, 10, 15, 20, 25, 30, 35, 11, 16, 21, 26, 31, 36, 41];
if ($shapeName === 'rec') {
    $available = [5, 10, 15, 20, 25, 30, 35];
}
if (!isIslandsAvailable($board, $available)) {
    return [];
}
```

```php {all|8-11|all}
function isIslandsAvailable(array $board, array $available): bool
{
    for ($i = 0; $i < 7; ++$i) {
        for ($j = 0; $j < 7; ++$j) {
            if ($board[$i][$j] !== 0) {
                continue;
            }
            $area = dfs($board, $i, $j);
            if (!in_array($area, $available, true)) {
                return false;
            }
        }
    }
    return true;
}
```

---
layout: section
---

## 可行解 Solution

## DFS 求最小岛屿面积

::right::

```php {all|13-14|16-20|all}
function dfs(array &$board, int $i, int $j): int
{
    // 越界
    if (!(0 <= $i && $i < 7 && 0 <= $j && $j < 7)) {
        return 0;
    }

    // 本例中, 0 为陆地
    if ($board[$i][$j] !== 0) {
        return 0;
    }

    // floodFill
    $board[$i][$j] = 2;

    return 1 +
        +dfs($board, $i - 1, $j)
        + dfs($board, $i + 1, $j)
        + dfs($board, $i, $j - 1)
        + dfs($board, $i, $j + 1);
}
```

---
layout: section
---

## 可行解 Solution

## 最外层的 BFS

一块一块尝试放置

::right::

```php {all|7-21|all}
public function aPuzzleEachDay($board, $allShapes): bool
{
    // some prepare work, queue, sort etc.
    // 积木名数组, 按解空间从小到大排序 (贪心: 预定义顺序)
    $names = array_keys($allShapes);
    $level = 1; // 当前搜索了几层, 初始化已经有了一层
    while (!$queue->isEmpty()) { // BFS 搜索
        $queueCount = $queue->count();
        $curShapes = $allShapes[$names[$level]] ?? [];
        if (empty($curShapes)) { // 防止没有解的情况发生
            return false;
        }

        foreach ($queue as $item) {
            if ($this->dealWithQueue($item)) { // 处理队列中的成员
                return true;
            }
        }

        $level++;
    }

    return false; // 队列为空还没有找到可行解
}
```

---

### 可行解: 回溯处理队列成员

```php {all}
protected function dealWithQueue(SplQueue $queue, array $curShapes, string $curName, int $level, array $allShapes)
{
    $cur = $queue->dequeue();
    // 当前积木的每种形状都尝试一下, 这里和初始化函数基本一致
    foreach ($curShapes as $k => $shape) {
        for ($i = 0; $i < 6; ++$i) {
            for ($j = 0; $j < 6; ++$j) {
                $newBoard = $this->dropOneShape($curName, $cur['board'], $shape, $i, $j);
                if (empty($newBoard)) { // 当前积木 ($name) 当前方向 ($k) 不可放入到当前位置 ($i, $j), 继续搜索
                    continue;
                }
                $cur['path'][] = [$i, $j, $curName, $k]; // 回溯
                if ($level === $this->maxLevel - 1) { // 已经放了 7 块, 当前积木可放, 即时判断是否搜索到可行解, 返回即可
                    dumpResult($cur['path'], $allShapes, $this->drops);
                    return true;
                }

                $queue->enqueue(['board' => $newBoard, 'path' => $cur['path']]);
                array_pop($cur['path']); // 回溯, 恢复现场
            }
        }
    }
}
```
