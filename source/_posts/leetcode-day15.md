---
title: leetcode-day15
date: 2020-05-29 11:51:28
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

### 题目[198.打家劫舍](https://leetcode-cn.com/problems/house-robber/)
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同
一晚上被小偷闯入，系统会自动报警。
给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

#### 用例
输入: \[1,2,3,1]
输出: 4

输入: \[2,7,9,3,1]
输出: 12
#### 解题思路
1. 动态规划，状态转移方程为`dp[i]=max(dp[i-2]+nums[i],dp[i-1])`

#### 代码
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:return 0
        n = len(nums)
        if n<2:return nums[0]
        dp=[0]*n
        dp[0]=nums[0]
        dp[1]=max(nums[0],nums[1])
        for i in range(2,n):
            dp[i]=max(dp[i-2]+nums[i],dp[i-1])
        return dp[-1]


class Solution2:
    def rob(self, nums: List[int]) -> int:
        prevmax=0
        currmax=0
        for num in nums:
            tmp=currmax
            currmax=max(prevmax+num,currmax)
            prevmax=tmp
        return currmax


k=Solution2()
print(k.rob([1,7,9,4]))
```

### 题目[213.打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻
的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。
#### 用例
输入: \[2,3,2]
输出: 3

输入: \[1,2,3,1]
输出: 4
#### 解题思路
考虑两种情况第一户不能取以及第二户不能取，再按照打家劫舍的方法计算就可以
#### 代码
```python
class Solution:
    #表是循环的，头和尾不能同时取
    def rob(self, nums: List[int]) -> int:
        #分析可得知，对于循环的表，头和尾不能同时取得，所以将其去掉
        if not nums: return 0
        def core(array):
            prev, cur = 0, 0
            for num in array:
                tmp=cur
                cur=max(prev+num,cur)
                prev=tmp
            return cur

        return max(core(nums[1:]),core(nums[:-1])) if len(nums)>1 else nums[0]


k=Solution()
print(k.rob([2,3,1]))
```

### 题目[337.打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“
房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

#### 解题思路
树形dp问题
#### 代码
```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def rob(self, root: TreeNode) -> int:
        def dp(node):
            if not node: return [0, 0]
            left = dp(node.left)
            right = dp(node.right)
            return [max(left) + max(right),node.val + left[0] + right[0]]

        return max(dp(root))
```

### 题目[518.零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)
给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。 


#### 用例
输入: amount = 5, coins = \[1, 2, 5]
输出: 4

输入: amount = 3, coins = \[2]
输出: 0
#### 解题思路
动态规划，完全背包问题
#### 代码
```python
class Solution:
    def change(self, amount: int, coins: List[int]) -> int:
        dp = [0] * (1 + amount)
        dp[0] = 1
        for coin in coins:
            for i in range(1, amount + 1):
                if i -coin>=0:
                    dp[i] += dp[i - coin]
        return dp[amount]
```