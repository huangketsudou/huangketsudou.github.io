---
title: leetcode-435
date: 2020-05-09 16:52:04
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

![图片](http://api.mtyqx.cn/api/random.php?7)
<!-- more -->


### 题目leetcode-433

一条基因序列由一个带有8个字符的字符串表示，其中每个字符都属于 "A", "C", "G", "T"中的任意一个。
假设我们要调查一个基因序列的变化。一次基因变化意味着这个基因序列中的一个字符发生了变化。
例如，基因序列由"AACCGGTT" 变化至 "AACCGGTA" 即发生了一次基因变化。
与此同时，每一次基因变化的结果，都需要是一个合法的基因串，即该结果属于一个基因库。
现在给定3个参数 — start, end, bank，分别代表起始基因序列，目标基因序列及基因库，请找出能够使起始基因序列变化为目标基因序列所需的最少变化次数。
如果无法实现目标变化，请返回 -1。
<br>注意:</br>
起始基因序列默认是合法的，但是它并不一定会出现在基因库中。所有的目标基因序列必须是合法的。
假定起始基因序列与目标基因序列是不一样的。


### 用例

输入: \[ \[1,2\], \[2,3\] \]

输出: 0

输入: \[ \[1,2\], \[1,2\], \[1,2\] \]

输出: 2

输入: \[ \[1,2\], \[2,3\], \[3,4\], \[1,3\] ]

输出: 1


### 解题思想

贪心以及动态规划
leetcode题解区：
https://leetcode-cn.com/problems/non-overlapping-intervals/solution/wu-zhong-die-qu-jian-by-leetcode/
https://leetcode-cn.com/problems/non-overlapping-intervals/solution/tan-xin-suan-fa-zhi-qu-jian-diao-du-wen-ti-by-labu/

###代码

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        n = len(intervals)
        if n < 2: return 0
        intervals.sort(key=lambda x: x[0])
        dp = [0] * n
        dp[0] = 1
        ans = 1
        for i in range(1, n):
            maximun = 0
            for j in range(i - 1, -1, -1):
                if not intervals[i][0] < intervals[j][1]:
                    maximun = max(dp[j], maximun)
            dp[i] = maximun + 1
            ans = max(ans, dp[i])
        return n - ans


class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        n = len(intervals)
        if n < 2: return 0
        intervals.sort(key=lambda x: x[1])
        dp = [0] * n
        dp[0] = 1
        ans = 1
        for i in range(1, n):
            maximun = 0
            for j in range(i - 1, -1, -1):
                if not intervals[i][0] < intervals[j][1]:
                    maximun = max(dp[j], maximun)
                    break
            dp[i] = max(maximun + 1, dp[i - 1])
            ans = max(ans, dp[i])
        return n - ans


class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if not intervals:
            return 0

        intervals = sorted(intervals, key=lambda x: x[1])

        ans = 0
        end = -float('inf')  # 结束时间
        for i in intervals:
            if i[0] >= end:
                ans += 1
                end = i[1]
        return len(intervals) - ans


class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        n = len(intervals)
        if n < 2: return 0
        intervals.sort(key=lambda x: x[0])
        end = intervals[0][1]
        prev = 0
        count = 0
        for i in range(1, n):
            if intervals[prev][1] > intervals[i][0]:
                if intervals[prev][1] > intervals[i][1]:
                    prev = i
                count += 1
            else:
                prev = i
        return count

```