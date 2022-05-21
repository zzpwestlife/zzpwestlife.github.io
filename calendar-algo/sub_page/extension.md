---
layout: statement
---
# Demo

---

## 可行解: 方案对比

| 顺序      |  方案         | 复杂度      | 时间   | 空间  | 实际尝试次数 | 备注                                         |
| -----------|------------- | ----------- | ------: | -----: | ------------: | --------------------------------------------: |
| 随机顺序 |最小岛屿剪枝 1 | 3.34E+24 | 6.37s   | 112MB | 12718962     | 还可以接受                                   |
| 随机顺序 |最小岛屿剪枝 2 | 2.04E+22 | 2.08s | 32MB  | 4637060      |                                              |
| 随机顺序 |最小岛屿剪枝 3 | 7.11E+24 | 5.02s  | 98MB  | 11375871     |                                              |
| 随机顺序 |所有岛屿剪枝 1 | 1.50E+20 | 1.19s  | 16MB  | 2622258      | 明显优化, 还不够稳定                         |
| 随机顺序 |所有岛屿剪枝 2 | 1.25E+21 | 1.06s  | 16MB  | 2389983      |                                              |
| 随机顺序 |所有岛屿剪枝 3 | 3.74E+21 | 1.82s  | 26MB  | 4673930      |                                              |
| 预定义顺序 |所有岛屿剪枝 | 1.04E+19 | 0.63s  | 8MB   | 1528080      | 优化效果明显,稳定输出                        |
| 贪心| 所有岛屿剪枝       | 2.30E+19 | 0.76s  | 10MB  | 1868136      | 比预定义排序略差 |

<style>
    table tr td:nth-child(4) {
        color: red;
    }

    table tr:nth-child(7) td:nth-child(7) {
        color: red;
    }

    table tr td {
        font-size: small;
    }

</style>

---
clicks: 9
---

# 拓展 Extension

<v-clicks>

- 一天只有一种解法么?
- 哪天解法最多, 哪天解法最少?
- 带星期的日历, 如何求解?
- 继续优化, 双路 BFS (-50%+)?
- 棋盘和积木块的设计?
- 类似游戏的求解?
- 视觉算法?

</v-clicks>

<div v-click-hide="4"><img v-click="3" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/calendar_with_weeks.png" style="height:60%;position:absolute;top:20%;left:40%" /></div>

<div v-click-hide="5"><img v-click="4" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/double-end-BFS.png" style="width:55%;position:absolute;top:30%;left:40%" /></div>

<div v-click-hide="7">
    <img v-click="6" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/sudoku.png" style="height:30%;position:absolute;top:20%;left:40%" />
    <img v-click="6" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/queens.jpeg" style="height:30%;position:absolute;top:20%;left:60%" />
    <img v-click="6" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/huarongdao.jpg" style="height:30%;position:absolute;top:55%;left:40%" />
    <img v-click="6" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/number-puzzle.webp" style="height:30%;position:absolute;top:55%;left:60%" />
</div>

<div v-click-hide="8"><img v-click="7" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/perfect_match.png" style="height:60%;position:absolute;top:20%;left:40%" /></div>

<div v-click-hide="9"><img v-click="8" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/impossible_1.png" style="height:60%;position:absolute;top:20%;left:40%" /></div>

<img v-click="9" src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/impossible_2.png" style="height:60%;position:absolute;top:20%;left:40%" />

---

<img src="https://picgo-1253542015.cos.ap-guangzhou.myqcloud.com/uPic/data_structure_relation.png" style="height:90%;position:absolute;top:5%;left:13%" />

---

# 参考资料

- 力扣中国官网
- 数据结构与算法之美
- 代码随想录
- 吴师兄学算法
- 花花酱刷题
- [完整代码](https://gitlab.futunn.com/joeyzou/joeyzou-demo/-/tree/master/calendar)

## 工具

- [slidev](https://cn.sli.dev/guide/why.html)
-[bricks](https://github.com/slidevjs/themes/tree/main/packages/theme-bricks)
- mermaid
- 懒人词云小程序
- sketch

---
layout: statement
---

# Thanks