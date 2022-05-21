---
layout: section
---

## 回溯和剪枝

backtrack & pruning

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/pruning.png" style="height:30%;position:absolute;top:40%;left:25%" />

::right::

```go {all|1|2-5|7-11|all}
func backtrack(路径, 选择列表) {
    if 满足终止条件 {
        收集结果 (路径添加至结果集)
        return
    }

    for 选择 :range 选择列表 {
        做选择
        backtrack(路径, 选择列表)
        回溯操作, 撤销选择
    }
}
```

1. 路径: 已经做出的选择;
2. 选择列表: 当前可以做出的选择;
3. 结束条件: 到达决策树底层 (叶子节点).

---

<!-- ## 全排列 46 -->

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/46.png" style="width:40%;position:absolute;top:30%;left:5%" />

<img v-click="1" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/46_0.png" style="width:40%;position:absolute;top:30%;left:50%" />

<img v-click="2" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/46_1.png" style="width:40%;position:absolute;top:30%;left:50%" />

<div v-click="3" style="width:40%;position:absolute;top:70%;left:50%">

1. 路径: 已经做出的选择 [2];

2. 选择列表: 当前可以做出的选择 [1, 3];

3. 结束条件: 到达决策树底层 (叶子节点), 选择列表为空的时候.

</div>

<img v-click="4" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/46_2.png" style="width:40%;position:absolute;top:30%;left:50%" />

---
layout: section
---

## 全排列 46 代码

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/46.png" style="width:40%;position:absolute;top:30%;left:5%" />

::right::

```go {all|5|6-9|10-19|11-13|14-18|all}
func permute(nums []int) (answer [][]int) {
    length := len(nums)
    used := make([]bool, length) // 路径, 已经做出的选择
    var dfs func(depth int, path []int)
    dfs = func(depth int, path []int) {
        if depth == length { // 结束条件, 用递归深度判断
            answer = append(answer, path)
            return
        }
        for i := 0; i < length; i++ { // 遍历选择列表
            if used[i] { // 选择列表不包含路径中的元素, 剪枝
                continue
            }
            path = append(path, nums[i]) // 路径记录已经做出的选择
            used[i] = true
            dfs(depth+1, path) // // 递归到下一层
            used[i] = false // 回溯发生在从深层结点回到浅层结点的过程，代码在形式上和递归之前是对称的
            path = path[:len(path)-1]
        }
    }

    dfs(0, []int{})
    return
}
```

---

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/deep_blue.jpeg" style="width:40%;position:absolute;top:30%;left:5%" />

<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/alpha_go.jpeg" style="width:40%;position:absolute;top:24%;left:50%" />

---

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/chess.gif" style="height:100%;position:absolute;top:0%;left:25%" />
