---
layout: section
---

# 岛屿问题 islands

::right::

LeetCode 200. 岛屿数量 (Easy)

LeetCode 463. 岛屿的周长 (Easy)

LeetCode 695. 岛屿的最大面积 (Medium)

LeetCode 827. 最大人工岛 (Hard)

<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/islands.png" style="height:50%;position:absolute;top:30%;left:30%" />

---

# 二叉树 -> 网格 DFS

```go
func traverse(root *TreeNode) {
    // 判断 base case
    if (root == nil) {
        return;
    }

    // 处理当前节点
    // 访问两个相邻结点：左子结点、右子结点
    traverse(root.Left);
    traverse(root.Right);
}
```

<img v-click="1" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/grid_adjacent.png" style="height:50%;position:absolute;top:24%;left:40%" />

<img v-click="2" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/grid_return.png" style="height:50%;position:absolute;top:24%;left:32%" />

---

```go {all|1|2-5|6-9|13-17|11|all}
void dfs(int[][] grid, int r, int c) {
    // 判断 base case
    if (!inArea(grid, r, c)) {
        return;
    }
    // 如果这个格子不是岛屿，直接返回
    if (grid[r][c] != 1) {
        return;
    }
    
    grid[r][c] = 2; // 将格子标记为「已遍历过」
    
    // 访问上、下、左、右四个相邻结点
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}

// 判断坐标 (r, c) 是否在网格中
boolean inArea(int[][] grid, int r, int c) {
    return 0 <= r && r < grid.length 
         && 0 <= c && c < grid[0].length;
}
```

<img v-click src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/islands_flag.gif" style="height:50%;position:absolute;top:30%;left:40%" />

---
layout: section
---

# 岛屿最大面积 695

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/695.png" style="height:50%;position:absolute;top:30%;left:2%" />

::right::

```go {all|3-10|14|15-17|20,21|18|20,21|all}
public int maxAreaOfIsland(int[][] grid) {
    int res = 0;
    for (int r = 0; r < grid.length; r++) {
        for (int c = 0; c < grid[0].length; c++) {
            if (grid[r][c] == 1) {
                int a = area(grid, r, c);
                res = Math.max(res, a);
            }
        }
    }
    return res;
}

int area(int[][] grid, int r, int c) {
    if (!inArea(grid, r, c) || grid[r][c] != 1) {
        return 0;
    }
    grid[r][c] = 2;
    
    return 1 + area(grid, r - 1, c) + area(grid, r + 1, c)
        + area(grid, r, c - 1) + area(grid, r, c + 1);
}
```
