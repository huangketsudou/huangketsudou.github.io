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

给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

注意:

可以认为区间的终点总是大于它的起点。
区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。



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