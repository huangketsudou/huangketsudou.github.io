---
title: leetcode周赛191
date: 2020-06-01 15:29:34
tags:
- leetcode
- 算法
- python
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->

### 题目[1464.数组中两元素的最大乘积](https://leetcode-cn.com/problems/maximum-product-of-two-elements-in-an-array/)
给你一个整数数组 nums，请你选择数组的两个不同下标 i 和 j，使 (nums[i]-1)*(nums[j]-1) 取得最大值。

请你计算并返回该式的最大值


#### 用例
```
输入：nums = [3,4,5,2]
输出：12 
```

```
输入：nums = [1,5,4,5]
输出：16
```
#### 解题思路
找两个最大值输出就好

#### 代码
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        nums.sort()
        return max((nums[0] - 1) * (nums[1] - 1), (nums[-2] - 1) * (nums[-1] - 1))


k=Solution()
print(k.maxProduct([3,4,5,2]))
```

### 题目[1465.切割后面积最大的蛋糕](https://leetcode-cn.com/problems/maximum-area-of-a-piece-of-cake-after-horizontal-and-vertical-cuts/)

矩形蛋糕的高度为 h 且宽度为 w，给你两个整数数组 horizontalCuts 和 verticalCuts，其中 horizontalCuts\[i] 是从矩形蛋糕顶部到第  i 个水平
切口的距离，类似地， verticalCuts\[j] 是从矩形蛋糕的左侧到第 j 个竖直切口的距离。

请你按数组 horizontalCuts 和 verticalCuts 中提供的水平和竖直位置切割后，请你找出 面积最大 的那份蛋糕，并返回其 面积 。由于答案可能是一个很大的
数字，因此需要将结果对 10^9 + 7 取余后返回。

#### 用例
```
输入：h = 5, w = 4, horizontalCuts = [1,2,4], verticalCuts = [1,3]
输出：4 
```

```
输入：h = 5, w = 4, horizontalCuts = [3,1], verticalCuts = [1]
输出：6
```

```
输入：h = 5, w = 4, horizontalCuts = [3], verticalCuts = [3]
输出：9
```

#### 解题思路
找最大的长和宽就好

#### 代码
```python
class Solution:
    def maxArea(self, h: int, w: int, horizontalCuts: List[int], verticalCuts: List[int]) -> int:
        prev = 0
        heights = set()
        for i in sorted(horizontalCuts):
            heights.add(i - prev)
            prev = i
        heights.add(h - prev)
        width = set()
        prev = 0
        for j in sorted(verticalCuts):
            width.add(j- prev)
            prev = j
        width.add(w - prev)
        heights = list(heights)
        width = list(width)
        heights.sort()
        width.sort()
        return heights[-1]*width[-1]
```

### 题目[1466.重新规划路线](https://leetcode-cn.com/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)
n 座城市，从 0 到 n-1 编号，其间共有 n-1 条路线。因此，要想在两座不同城市之间旅行只有唯一一条路线可供选择（路线网形成一颗树）。去年，交通运输部决定重新规划路线，以改变交通拥堵的状况。
路线用 connections 表示，其中 connections\[i] = \[a, b] 表示从城市 a 到 b 的一条有向路线。

今年，城市 0 将会举办一场大型比赛，很多游客都想前往城市 0 。
请你帮助重新规划路线方向，使每个城市都可以访问城市 0 。返回需要变更方向的最小路线数。
题目数据 保证 每个城市在重新规划路线方向后都能到达城市 0 。


#### 用例
```
输入：n = 6, connections = [[0,1],[1,3],[2,3],[4,0],[4,5]]
输出：3
```
```
输入：n = 5, connections = [[1,0],[1,2],[3,2],[3,4]]
输出：2
```

#### 解题思路
从0出发，bfs遍历

#### 代码
```python

class Solution:
    def minReorder(self, n: int, connections: List[List[int]]) -> int:
        outnode = defaultdict(list)
        innode = defaultdict(list)
        for start,end in connections:
            outnode[start].append(end)
            innode[end].append(start)
        stack = deque()
        stack.append(0)
        visited = set()
        ans = 0
        while stack:
            node = stack.popleft()
            if node in outnode:
                for k in outnode[node]:
                    if k not in visited:
                        stack.append(k)
                        ans += 1
            if node in innode:
                for g in innode[node]:
                    stack.append(g)
            visited.add(node)
        return ans

k=Solution()
print(k.minReorder(n = 3, connections = [[1,0],[2,0]]))
```

### 题目[1467.两个盒子中球的颜色数相同的概率](https://leetcode-cn.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/)
桌面上有 2n 个颜色不完全相同的球，球上的颜色共有 k 种。给你一个大小为 k 的整数数组 balls ，其中 balls\[i] 是颜色为 i 的球的数量。
所有的球都已经 随机打乱顺序 ，前 n 个球放入第一个盒子，后 n 个球放入另一个盒子（请认真阅读示例 2 的解释部分）。

注意：这两个盒子是不同的。例如，两个球颜色分别为 a 和 b，盒子分别为 [] 和 ()，那么 \[a] (b) 和 \[b] (a) 这两种分配方式是不同的（请认真阅读示例 1 的解释部分）。
请计算「两个盒子中球的颜色数相同」的情况的概率。

#### 用例
```
输入：balls = [1,1]
输出：1.00000
```

```
输入：balls = [2,1,1]
输出：0.66667
```
#### 解题思路
一个排列组合问题，看下这个[视频](https://zhuanlan.zhihu.com/p/36910867)
dfs遍历
题解区题解-[sth4nothong](https://leetcode-cn.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/solution/python3-mei-ju-2372ms-by-sth4nothing/)
枚举第一个盒子的所有情况，计算两个盒子中球的颜色数相同的排列总数，从而求得概率。耗时2372ms。

python的函数itertools.product可以求多个列表的笛卡尔积，利用这个函数枚举第一个盒子中各种颜色的球的数目。
然后判断两个盒子中球的颜色种类是否一样。如果一样，则计算当前情况下的排列数目。否则跳过。
假如用数组nums表示某个盒子中各种颜色的球的个数，由排列组合的知识，可以得到该盒子中球的排列数目为:
    ![公式](../img/公式1.png)

将两个盒子中的排列数目求积就是当前情况下的排列数。
将所有两个盒子中颜色种类相同的排列求和就是颜色数相同的所有情况。
概率 = 两个盒子中球的颜色数相同的排列总数 / 球的所有排列方案总数


#### 代码

```python
class Solution:
    def getProbability(self, balls: List[int]) -> float:
        import sys
        sys.setrecursionlimit(10000000)
        import functools
        @functools.lru_cache(None)
        def c(a):
            if a == 0:
                return 1
            return a * c(a - 1)

        total = sum(balls)
        zong = c(total)
        for b in balls:
            zong //= c(b)

        cc = c(total // 2)
        import functools
        @functools.lru_cache(None)
        def dfs(pre, i, a, b, px, py):
            if i == len(balls):
                if pre == total / 2:
                    if a == b:
                        return cc / px * cc / py
                return 0
            res = 0
            for j in range(balls[i] + 1):
                if j == 0:
                    res += dfs(pre, i + 1, a, b + 1, px * c(j), py * c(balls[i] - j))
                elif j != balls[i]:
                    res += dfs(pre + j, i + 1, a + 1, b + 1, px * c(j), py * c(balls[i] - j))
                else:
                    res += dfs(pre + j, i + 1, a + 1, b, px * c(j), py * c(balls[i] - j))
            return res

        res = dfs(0, 0, 0, 0, 1, 1)
        return res / zong
```

```python
import itertools
import math
from typing import *


class Solution:
    def getProbability(self, balls: List[int]) -> float:
        def arrangement(nums: Sequence[int]):
            s = sum(nums)
            t = math.factorial(s)
            for b in nums:
                t //= math.factorial(b)
            return t

        def count(nums: Sequence[int]):
            return sum(b != 0 for b in nums)

        n = sum(balls) // 2
        total = arrangement(balls)
        ans = 0
        for left in itertools.product(*(range(b + 1) for b in balls)):
            if sum(left) != n:
                continue
            right = tuple(b - v for b, v in zip(balls, left))
            if count(left) == count(right):
                ans += arrangement(left) * arrangement(right)
        return ans / total

```