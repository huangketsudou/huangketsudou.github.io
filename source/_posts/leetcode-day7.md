---
title: leetcode-day7
date: 2020-05-19 14:04:52
tags:
- leetcode
- 算法
- python
- 动态规划
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](#http://api.mtyqx.cn/api/random.php?x)
<!-- more -->

### 题目473[火柴拼正方形](https://leetcode-cn.com/problems/matchsticks-to-square/)
还记得童话《卖火柴的小女孩》吗？现在，你知道小女孩有多少根火柴，请找出一种能使用所有火柴拼成一个正方形的方法。不能折断火柴，可以把火柴连接起来，并且每根火柴都要用到。

输入为小女孩拥有火柴的数目，每根火柴用其长度表示。输出即为是否能用所有的火柴拼成正方形。
#### 用例
输入: \[1,1,2,2,2]
输出: true


#### 解题思路
1. dfs遍历，用四个数字存每个边已构成的边长，决定每个边应属于哪个边
2. dfs遍历，但采用状态压缩法记录
#### 代码
```python
class Solution:
    # 确定某一根火柴要放在那个正方形边的组中
    def makesquare(self, nums: List[int]) -> bool:
        if not nums: return False
        n = len(nums)
        permiter = sum(nums)
        possible_side = permiter // 4
        if possible_side * 4 != permiter:
            return False
        nums.sort(reverse=True)
        sums = [0 for _ in range(4)]

        def dfs(idx):
            if idx == n:
                return sums[0] == sums[1] == sums[2] == possible_side
            for i in range(4):
                if sums[i] + nums[idx] <= possible_side:
                    sums[i] += nums[idx]
                    if dfs(idx + 1):
                        return True
                    sums[i] -= nums[idx]
            return False

        return dfs(0)


class Solution:
    # 状态压缩，mask掩码，1表示这根火柴还没有被用过，可以使用
    def makesquare(self, nums: List[int]) -> bool:
        if not nums: return False
        n = len(nums)
        perimeter = sum(nums)
        possible_side = perimeter // 4
        if possible_side * 4 == perimeter: return False
        memo = {}
        def dfs(mask, sides_done):
            total = 0
            for i in range(n - 1, -1, -1):#这里的for循环作用是计算所有已经被用掉的火柴边的总长度
                if not (mask & (1 << i)):
                    total += nums[n - 1 - i]
            if total > 0 and total % possible_side == 0:
                sides_done += 1
            if sides_done == 3:
                return True
            if (mask, sides_done) in memo:
                return memo[(mask, sides_done)]
            ans = False
            c = int(total / possible_side)
            rem = possible_side * (c + 1) - total#计算当前要构成下一完整的边需要多长的火柴
            for i in range(n - 1, -1, -1):
                if nums[n - 1 - i] <= rem and mask & (1 << i):
                    if dfs(mask ^ (1 << i), sides_done):
                        ans = True
                        break
            memo[(mask, sides_done)] = ans
            return ans

        return dfs((1 << n) - 1, 0)
```


### 题目474[一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)
在计算机界中，我们总是追求用有限的资源获取最大的收益。
现在，假设你分别支配着m个0和n个1。另外，还有一个仅包含0和1字符串的数组。
你的任务是使用给定的m个0和n个1，找到能拼出存在于数组中的字符串的最大数量。每个0和1至多被使用一次。

注意:给定0和1的数量都不会超过100。
给定字符串数组的长度不会超过600。


#### 用例
输入: Array = {"10", "0001", "111001", "1", "0"}, m = 5, n = 3
输出: 4

输入: Array = {"10", "0", "1"}, m = 1, n = 1
输出: 2

#### 解题思路
二维完全背包问题，采用动态规划进行处理

#### 代码
```python
class Solution:
    ##二维完全背包问题，且包内物品只能用一次
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        dp = [[0] * (n + 1) for _ in range(m + 1)]
        for s in strs:
            zeros=s.count('0')
            ones=s.count('1')
            for i in range(m,zeros-1,-1):
                for j in range(n,ones-1,-1):
                    dp[i][j]=max(1+dp[i-zeros][j-ones],dp[i][j])
        return dp[-1][-1]

```

### 题目477[汉明距离总和](https://leetcode-cn.com/problems/total-hamming-distance/)
两个整数的汉明距离指的是这两个数字的二进制数对应位不同的数量。
计算一个数组中，任意两个数之间汉明距离的总和。
#### 用例
输入: 4, 14, 2

输出: 6

#### 解题思路
汉明距离等于两个数二进制表示中对应位置不同的数量。假设数组中的每个数都表示为 k 位的二进制数（高位补 0），那么我们可以发现，要计算数组中任意两个数的汉明距离的总和，可以先算出数组中任意两个数二进制第 i 位的汉明距离的总和，在将所有的 k 位之和相加。也就是说，二进制中的每一位都是可以独立计算的。

因此，我们考虑数组中每个数二进制的第 i 位，假设一共有 t 个 0 和 n - t 个 1，那么显然在第 i 位的汉明距离的总和为 t * (n - t)。

#### 代码

```python
from typing import List


class Solution:
    def totalHammingDistance(self, nums: List[int]) -> int:
        return sum((b.count('0') * b.count('1')) for b in zip(*map('{:032b}'.format, nums)))

```