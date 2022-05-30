---
layout: fact
---

# 树 图 BFS DFS <br> Tree, Graph

---

# 树及其遍历 <br> Tree & Traversal

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/binary_tree.png" style="height:60%;position:absolute;top:30%;left:30%" class="rounded shadow" />

---

## 广度 vs. 深度优先遍历 (BFS&DFS)

<img v-click-hide src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/BFS&DFS.png" style="height:50%;position:absolute;top:30%;left:20%" class="rounded shadow" />
<img v-click='1' src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/BFS.gif" style="height:50%;position:absolute;top:30%;left:20%" class="rounded shadow" />
<img v-click='2' src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/DFS.gif" style="height:50%;position:absolute;top:30%;left:50%" class="rounded shadow" />

---

# 广度优先遍历 BFS

```go {all|3|4|7-9,16|11-15|6,19|all}
func levelOrder(root *TreeNode) [][]int {
    var res [][]int // 结果集
    queue := list.New() // 辅助队列
    queue.PushBack(root) // 队列初始化, 压入根节点
    var tmpArr []int // 存储当前层的所有节点
    for queue.Len() > 0 { // 队列不为空, 进行处理
        length := queue.Len() // 临时变量, 存储当前层队列长度
        for i := 0; i < length; i++ { // 遍历队列, 队列中的元素全部出队
            node := queue.Remove(queue.Front()).(*TreeNode) // 单个节点出队
            tmpArr = append(tmpArr, node.Val) // 节点值存储到当前层临时数组中
            if node.Children != nil { // 子树如果不为空, 所有子节点压入队列
                for _, child := range node.Children {
                    queue.PushBack(child)
                }
            }
        }
        res = append(res, tmpArr) // 当前层的节点值数组存入结果集
        tmpArr = []int{} // 恢复临时数组为空
    }
    return res
}
```

---
layout: section
---

## 二叉树的层序遍历 102

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/102.png" style="width:40%;position:absolute;top:20%;left:5%" class="rounded shadow" />

::right::

```go {all|11-16|all}
func levelOrder(root *TreeNode) [][]int {
    var res [][]int
    queue := list.New()
    queue.PushBack(root)
    var tmpArr []int
    for queue.Len() > 0 {
        length := queue.Len()
        for i := 0; i < length; i++ {
            node := queue.Remove(queue.Front()).(*TreeNode)
            tmpArr = append(tmpArr, node.Val)
            if node.Left != nil {
                queue.PushBack(node.Left)
            }
            if node.Right != nil {
                queue.PushBack(node.Right)
            }
        }
        res = append(res, tmpArr)
        tmpArr = []int{}
    }

    return res
}
```

---

# 深度优先遍历 DFS

## 递归三要素

<br>

```go {all|1-2|3-6|8-11|all}
// 1. 方法定义
func preOrder(root *TreeNode, values *[]int) {
    // 2. 递归终止条件
    if root == nil {
        return
    }

    // 3. 根左右 (前中后序遍历只有这里有区别)
    *values = append(*values, root.Val)
    preOrder(root.Left, values)
    preOrder(root.Right, values)
}
```

---
layout: section
---

## 二叉树的前序遍历 144

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/144.png" style="width:40%;position:absolute;top:28%;left:5%" class="rounded shadow" />

::right::

```go {all|7-8|9-12|14-17|all}
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

// 入参为树的根节点, 以及结果集, 引用返回遍历结果
func preOrder(root *TreeNode, values *[]int) {
    // 递归终止条件
    if root == nil {
        return
    }

    // 根左右
    *values = append(*values, root.Val)
    preOrder(root.Left, values)
    preOrder(root.Right, values)
}
```

---
layout: section
---

## 二叉树的最大深度 104

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/104.png" style="width:35%;position:absolute;top:20%;left:5%" class="rounded shadow" />

::right::

## BFS

<v-click>

```go {all|7|10,18|19|all}
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    queue := list.New()
    queue.PushBack(root)
    depth := 0
    for queue.Len() > 0 {
        length := queue.Len()
        for i := 0; i < length; i++ {
            node := queue.Remove(queue.Front()).(*TreeNode)
            if node.Left != nil {
                queue.PushBack(node.Left)
            }
            if node.Right != nil {
                queue.PushBack(node.Right)
            }
        }
        depth++
    }
    return depth
}
```

</v-click>

---
layout: section
---

## 二叉树的最大深度 104

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/104.png" style="width:35%;position:absolute;top:20%;left:5%" class="rounded shadow" />

::right::

## DFS

<v-click>

```go {all|3|4-6|8-10|all}
func maxDepth(root *TreeNode) int {
    var dfs func(node *TreeNode) int
    dfs = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        left := float64(dfs(node.Left))
        right := float64(dfs(node.Right))
        return int(math.Max(left, right)) + 1
    }

    return dfs(root)
}
```

</v-click>

---
layout: section
---

## 树 图 BFS DFS

## 小结

::right::

- 深度优先遍历
- 广度优先遍历

BFS: 队列

DFS: 栈

图的遍历: 辅助备忘录
