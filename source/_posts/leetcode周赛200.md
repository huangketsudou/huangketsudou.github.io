---
title: leetcode周赛200
date: 2020-08-02 14:04:33
tags:
categories:
declare:
comments:
---

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->


### 题目[5475.统计好三元组](https://leetcode-cn.com/contest/weekly-contest-200/problems/count-good-triplets/)
给你一个整数数组 arr ，以及 a、b 、c 三个整数。请你统计其中好三元组的数量。

如果三元组 (arr[i], arr[j], arr[k]) 满足下列全部条件，则认为它是一个 好三元组 。

0 <= i < j < k < arr.length
|arr[i] - arr[j]| <= a
|arr[j] - arr[k]| <= b
|arr[i] - arr[k]| <= c
其中 |x| 表示 x 的绝对值。

返回 好三元组的数量 。

#### 用例
输入：arr = [3,0,1,1,9,7], a = 7, b = 2, c = 3
输出：4

输入：arr = [1,1,2,2,3], a = 0, b = 0, c = 1
输出：0

#### 解题思路
暴力求解

#### 代码
```python
class Solution:
    def countGoodTriplets(self, arr: List[int], a: int, b: int, c: int) -> int:
        ans = 0
        n = len(arr)
        for i in range(n - 2):
            for j in range(i+1,n-1):
                for k in range(j+1,n):
                    if abs(arr[i]-arr[j])<=a and abs(arr[j]-arr[k])<=b and abs(arr[i]-arr[k])<=c:
                        ans+=1
        return ans
```

### 题目[5476. 找出数组游戏的赢家](https://leetcode-cn.com/contest/weekly-contest-200/problems/find-the-winner-of-an-array-game/)

给你一个由 不同 整数组成的整数数组 arr 和一个整数 k 。

每回合游戏都在数组的前两个元素（即 arr[0] 和 arr[1] ）之间进行。比较 arr[0] 与 arr[1] 的大小，较大的整数将会取得这一回合的胜利并保留在位置 0 ，较小的整数移至数组的末尾。当一个整数赢得 k 个连续回合时，游戏结束，该整数就是比赛的 赢家 。

返回赢得比赛的整数。

题目数据 保证 游戏存在赢家。

#### 用例
输入：arr = [3,2,1], k = 10
输出：3

输入：arr = [2,1,3,5,4,6,7], k = 2
输出：5

#### 解题思路
对于某位置i处的值，如果前面有比他小的值，那么它会赢一次，之后再比较后面是否有比它小的，如果有，胜利次数加一，
如果没有，胜利次数变为1，变换当前目标值为比他大的值，重复

#### 代码
```python
class Solution:
    def getWinner(self, arr: List[int], k: int) -> int:
        n = len(arr)
        if k >= n:
            return max(arr)
        time = 0
        prev = arr[0]
        for i in range(1, n):
            if arr[i] < prev:
                time += 1
            else:
                time = 1
                prev = arr[i]
            if time == k:
                return prev
        return prev
```

### 题目[5477. 排布二进制网格的最少交换次数](https://leetcode-cn.com/contest/weekly-contest-200/problems/minimum-swaps-to-arrange-a-binary-grid/)

给你一个 n x n 的二进制网格 grid，每一次操作中，你可以选择网格的 相邻两行 进行交换。

一个符合要求的网格需要满足主对角线以上的格子全部都是 0 。

请你返回使网格满足要求的最少操作次数，如果无法使网格符合要求，请你返回 -1 。

主对角线指的是从 (1, 1) 到 (n, n) 的这些格子。
#### 用例
输入：grid = [[0,0,1],[1,1,0],[1,0,0]]
输出：3

输入：grid = [[0,1,1,0],[0,1,1,0],[0,1,1,0],[0,1,1,0]]
输出：-1

输入：grid = [[1,0,0],[1,1,0],[1,1,1]]
输出：0
#### 解题思路
读题：这道题竞赛的时候读错题了

求解每一行从后向前的连续0个数，之后冒泡排序

#### 代码
```python
class Solution:
    def minSwaps(self, grid: List[List[int]]) -> int:
        if not grid: return 0
        # 统计每一行 从右向左连续0的个数
        n = len(grid)
        zero_nums = []
        for i in range(n):
            j = n - 1
            while j >= 0 and grid[i][j] == 0: j -= 1
            zero_nums.append(n - 1 - j)
        # 贪心算法，从上到下查找满足条件的最小下标，即为交换到当前行的次数
        cnt = 0
        for i in range(n - 1):
            need_zeros = n - 1 - i

            j = i
            while j < len(zero_nums) and zero_nums[j] < need_zeros: j += 1

            # 没找到则说明不满足条件
            if j == len(zero_nums): return -1

            # 增加交换次数
            cnt += j - i
            # 交换数值
            while j > i:
                zero_nums[j], zero_nums[j-1]= zero_nums[j-1], zero_nums[j]
                j -= 1
        return cnt
```

### 题目[5478. 最大得分](https://leetcode-cn.com/contest/weekly-contest-200/problems/get-the-maximum-score/)
你有两个 有序 且数组内元素互不相同的数组 nums1 和 nums2 。

一条 合法路径 定义如下：

选择数组 nums1 或者 nums2 开始遍历（从下标 0 处开始）。
从左到右遍历当前数组。
如果你遇到了 nums1 和 nums2 中都存在的值，那么你可以切换路径到另一个数组对应数字处继续遍历（但在合法路径中重复数字只会被统计一次）。
得分定义为合法路径中不同数字的和。

请你返回所有可能合法路径中的最大得分。

由于答案可能很大，请你将它对 10^9 + 7 取余后返回。

#### 用例
输入：nums1 = [2,4,5,8,10], nums2 = [4,6,8,9]
输出：30

输入：nums1 = [1,3,5,7,9], nums2 = [3,5,100]
输出：109

输入：nums1 = [1,2,3,4,5], nums2 = [6,7,8,9,10]
输出：40
#### 解题思路
双指针，比较后方的比较大的值就好

#### 代码
```python
class Solution:
    def maxSum(self, nums1: List[int], nums2: List[int]) -> int:
        n = len(nums1)
        m = len(nums2)
        i = n - 1
        j = m - 1
        set1 = set(nums1)
        set2 = set(nums2)
        common = set1 & set2
        prev1 = 0
        prev2 = 0
        while i >= 0 and j >= 0:
            while i >= 0 and nums1[i] not in common:
                prev1 += nums1[i]
                i -= 1
            while j >= 0 and nums2[j] not in common:
                prev2 += nums2[j]
                j -= 1
            if nums1[i] == nums2[j]:
                MAX = max(prev1, prev2) + nums2[j]
                prev1 = prev2 = MAX % (10**9+7)
            i -= 1
            j -= 1
        while i >= 0:
            prev1 += nums1[i]
            i -= 1
        while j >= 0:
            prev2 += nums2[j]
            j -= 1
        return max(prev1, prev2) % (10**9+7)
```
