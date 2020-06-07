---
title: leetcode周赛192
date: 2020-06-07 16:46:06
tags:
- leetcode
- 算法
- python
categories:
- 程序人生
- 算法
---

![图片](http://api.mtyqx.cn/api/random.php?661)
<!-- more -->


### 题目[5428.重新排列数组](https://leetcode-cn.com/problems/shuffle-the-array/)
给你一个数组 nums ，数组中有 2n 个元素，按 [x1,x2,...,xn,y1,y2,...,yn] 的格式排列。

请你将数组按 [x1,y1,x2,y2,...,xn,yn] 格式重新排列，返回重排后的数组。

#### 用例
```
输入：nums = [2,5,1,3,4,7], n = 3
输出：[2,3,5,4,1,7] 
```

#### 解题思路
模拟

#### 代码
```python
class Solution:
    def shuffle(self, nums: List[int], n: int) -> List[int]:
        length=len(nums)//2
        ans = []
        for i in range(length):
            ans.append(nums[i])
            ans.append(nums[i+length])
        return ans
```

### 题目[5429.数组中的 k 个最强值](https://leetcode-cn.com/problems/the-k-strongest-values-in-an-array/)
给你一个整数数组 arr 和一个整数 k 。

设 m 为数组的中位数，只要满足下述两个前提之一，就可以判定 arr[i] 的值比 arr[j] 的值更强：

1. |arr[i] - m| > |arr[j] - m|
2. |arr[i] - m| == |arr[j] - m|，且 arr[i] > arr[j]
请返回由数组中最强的 k 个值组成的列表。答案可以以 任意顺序 返回。

中位数 是一个有序整数列表中处于中间位置的值。形式上，如果列表的长度为 n ，那么中位数就是该有序列表（下标从 0 开始）中位于 ((n - 1) / 2) 的元素。

例如 arr = [6, -3, 7, 2, 11]，n = 5：数组排序后得到 arr = [-3, 2, 6, 7, 11] ，数组的中间位置为 m = ((5 - 1) / 2) = 2 ，中位数 arr[m] 的值为 6 。
例如 arr = [-7, 22, 17, 3]，n = 4：数组排序后得到 arr = [-7, 3, 17, 22] ，数组的中间位置为 m = ((4 - 1) / 2) = 1 ，中位数 arr[m] 的值为 3 。


#### 用例
```
输入：arr = [1,2,3,4,5], k = 2
输出：[5,1]
```

#### 解题思路
排序就好

#### 代码
```python
from typing import List


class Solution:
    def getStrongest(self, arr: List[int], k: int) -> List[int]:
        arr.sort()
        length = len(arr)
        m = arr[(length - 1) // 2]
        arr.sort(key=lambda x:(abs(x-m),x),reverse=True)
        return arr[:k]
```


### 题目[5430.设计浏览器历史记录](https://leetcode-cn.com/problems/design-browser-history/)
你有一个只支持单个标签页的 浏览器 ，最开始你浏览的网页是 homepage ，你可以访问其他的网站 url ，也可以在浏览历史中后退 steps 步或前进 steps 步。

请你实现 BrowserHistory 类：

1. BrowserHistory(string homepage) ，用 homepage 初始化浏览器类。
2. void visit(string url) 从当前页跳转访问 url 对应的页面  。执行此操作会把浏览历史前进的记录全部删除。
3. string back(int steps) 在浏览历史中后退 steps 步。如果你只能在浏览历史中后退至多 x 步且 steps > x ，那么你只后退 x 步。请返回后退 至多 steps 步以后的 url 。
4. string forward(int steps) 在浏览历史中前进 steps 步。如果你只能在浏览历史中前进至多 x 步且 steps > x ，那么你只前进 x 步。请返回前进 至多 steps步以后的 url 。

#### 解题思路
栈和指针

#### 代码
```python
from collections import deque


class BrowserHistory:

    def __init__(self, homepage: str):
        self.history = deque()
        self.history.append(homepage)
        self.size = 1
        self.currentidx = 0

    def visit(self, url: str) -> None:
        while self.currentidx + 1 != self.size:
            self.history.pop()
            self.size -= 1
        self.history.append(url)
        self.size += 1
        self.currentidx += 1

    def back(self, steps: int) -> str:
        tmp = max(0, self.currentidx - steps)
        self.currentidx = tmp
        return self.history[self.currentidx]

    def forward(self, steps: int) -> str:
        tmp = min(self.size - 1, self.currentidx + steps)
        self.currentidx = tmp
        return self.history[self.currentidx]
```

### 题目[5431.给房子涂色 III]()

在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不需要被重新涂色。

我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = [1,2,2,3,3,2,1,1] ，它包含 5 个街区  [{1}, {2,2}, {3,3}, {2}, {1,1}] 。）

给你一个数组 houses ，一个 m * n 的矩阵 cost 和一个整数 target ，其中：

- houses[i]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
- cost[i][j]：是将第 i 个房子涂成颜色 j+1 的花费。
请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。

#### 用例
```
输入：houses = [0,0,0,0,0], cost = [[1,10],[10,1],[10,1],[1,10],[5,1]], m = 5, n = 2, target = 3
输出：9
```

#### 解题思路
1. 思路一：dfs遍历，因为会有超时，所以需要用一个字典进行缓存
2. 思路二：动态规划
    dp[i][j][k]表示i个房子，组成j个街区，并且以颜色k作为最后一个房子的颜色的费用

#### 代码
```python
class Solution(object):
    def minCost(self, hs, cost, m, n, target):
        """
        :type houses: List[int]
        :type cost: List[List[int]]
        :type m: int
        :type n: int
        :type target: int
        :rtype: int
        """
        self.n = n

        dp = {}

        def dfs(index, curc, pre):
            if index == len(hs):
                if curc == target: return 0
                return float('inf')
            if (index, curc, pre) in dp:
                return dp[index, curc, pre]
            if hs[index] != 0:
                return dfs(index + 1, curc + (hs[index] != pre), hs[index])
            res = float('inf')
            for color in range(1, self.n + 1):
                res = min(res, cost[index][color - 1] + dfs(index + 1, curc + (color != pre), color))
            dp[index, curc, pre] = res
            return res

        r = dfs(0, 0, -1)
        return r if r != float('inf') else -1


class Solution:
    def minCost(self, houses: List[int], cost: List[List[int]], m: int, n: int, target: int) -> int:
        le = len(houses)
        INF = float('inf')

        dp = [[[INF]*(n+1) for _ in range(target+1)] for _ in range(le+1)]
        dp[0][0] = [0]*(n+1)

        for i in range(1, le+1):
            for j in range(1, target+1):
                for k in range(1, n+1):
                    nowc = houses[i-1]

                    if nowc==0:
                        for kk in range(1, n+1):
                            if kk!=k:
                                dp[i][j][k] = min(dp[i][j][k], dp[i-1][j-1][kk]+cost[i-1][k-1])
                            else:
                                dp[i][j][k] = min(dp[i-1][j][kk]+cost[i-1][k-1], dp[i][j][k])


                    else:
                        if k!=nowc:
                            dp[i][j][nowc] = min(dp[i-1][j-1][k], dp[i][j][nowc])
                        else:
                            dp[i][j][nowc] = min(dp[i][j][nowc], dp[i-1][j][nowc])
        res = min(dp[-1][-1])
        return res if res!=INF else -1
```