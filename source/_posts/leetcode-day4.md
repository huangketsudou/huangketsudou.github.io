---
title: leetcode-day4
date: 2020-05-15 10:10:02
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

![图片](http://api.mtyqx.cn/api/random.php?56)
<!-- more -->


### 题目560[和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)
给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。
#### 用例
输入:nums = \[1,1,1], k = 2

输出: 2 , \[1,1] 与 \[1,1] 为两种不同的情况。

#### 解题思路
利用前缀和或者暴力求解都可以


#### 代码
```python
from collections import defaultdict

class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        mapping=defaultdict(int)
        mapping[0]=1
        prev=0
        ans=0
        for i in range(len(nums)):
            prev+=nums[i]
            ans+=mapping[prev-k]
            mapping[prev]+=1
        return

class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        #超时
        n=len(nums)
        ans=0
        for i in range(n):
            prev=0
            for j in range(i,n):
                prev+=nums[j]
                if prev==k:
                    ans+=1
        return ans
```


### 题目457[环形数组循环](https://leetcode-cn.com/problems/circular-array-loop/)
给定一个含有正整数和负整数的环形数组nums。如果某个索引中的数k为正数，则向前移动k个索引
。相反，如果是负数(-k)，则向后移动k个索引。因为数组是环形的，所以可以假设最后一个
元素的下一个元素是第一个元素，而第一个元素的前一个元素是最后一个元素。

确定nums中是否存在循环（或周期）。循环必须在相同的索引处开始和结束并且循环长度>1
。此外，一个循环中的所有运动都必须沿着同一方向进行。换句话说，一个循环中不能同时包括向
前的运动和向后的运动


#### 用例
输入：\[2,-1,1,2,2]
输出：true

输入：\[-1,2]
输出：false

输入：\[-2,1,-1,-2,-2]
输出：false
#### 解题思路
快慢指针求解，由于链表中可能不止一个所有需要对所有节点至少访问一遍，对已经访问
过的节点可以将其置为0，可以将复杂度降到O(N)

#### 代码
```python
class Solution:
    # O(N)
    def circularArrayLoop(self, nums: List[int]) -> bool:
        n = len(nums)
        if n == 0: return False
        Next = lambda x: (x + nums[x]) % n
        for i in range(n):
            if nums[i] == 0: continue
            fast = slow = i
            slow = Next(slow)
            fast = Next(Next(fast))
            while fast != slow:
                slow = Next(slow)
                fast = Next(Next(fast))
            if slow == Next(slow):
                nums[slow] = 0
                continue
            nxt = Next(slow)
            while fast != nxt:
                if (nums[slow] < 0) != (nums[nxt] < 0):
                    break
                slow = nxt
                nxt = Next(slow)
            else:
                return True
            while nums[fast] != 0:
                nxt = Next(fast)
                nums[fast] = 0
                fast = nxt
        return False
        
class Solution:
#O(N*N)
    def circularArrayLoop(self, nums: List[int]) -> bool:
        n = len(nums)
        if n==0:return False
        for i in range(n):
            fast = slow = i
            slow = (slow + nums[slow]) % n
            fast = (fast + nums[fast]) % n
            fast = (fast + nums[fast]) % n
            while fast != slow:
                slow = (slow + nums[slow]) % n
                fast = (fast + nums[fast]) % n
                fast = (fast + nums[fast]) % n

            if slow == ((slow + nums[slow]) % n):
                continue
            nxt = (slow + nums[slow]) % n
            while fast != nxt:
                if (nums[slow] < 0) != (nums[nxt] < 0):
                    break
                slow = nxt
                nxt = (slow + nums[slow]) % n
            else:
                return True
        return False
```


### 题目462[最少移动次数使数组元素相等 II](https://leetcode-cn.com/problems/minimum-moves-to-equal-array-elements-ii/)
给定一个非空整数数组，找到使所有数组元素相等所需的最小移动数，其中每次移动可将选定的一个元素加1或减1。 您可以假设数组的长度最多为10000。


#### 用例
输入:\[1,2,3]

输出:2

#### 解题思路
- 使用排序寻找中位数

假设最终数组a中的每个数均为x，那么需要移动的次数即为 |a\[0] - x| + 
|a\[1] - x| + ... + |a\[n-1] - x|。如果我们把数组 a 中的每个数看成水平轴上的一个点
，那么根据上面的移动次数公式，我们需要找到在水平轴上找到一个点 x，使得这 N个点到x的距
离之和最小。

这是一个经典的数学问题，当x为这个N个数的中位数时，可以使得距离最小。具体地，若N为奇数
，则x必须为这N个数中的唯一中位数；若 N 为偶数，中间的两个数为p和q，中位数为(p + q)/ 2，
此时x只要是区间\[p, q]中的任意一个数即可。

- 可以利用快速选择来实现快速查找中位数

#### 代码

```python
from typing import List
from functools import reduce
class Solution:
    def minMoves2(self, nums: List[int]) -> int:
        nums.sort()
        ans = 0
        k = nums[len(nums) // 2]
        for v in nums:
            ans += abs(v - k)
        return ans
```
```python
class Solution:
    def minMoves2(self, nums: List[int]) -> int:
        ans=0
        median=self.select(nums,0,len(nums)-1,len(nums)//2)
        for v in nums:
            ans+=abs(v-median)
        return ans


    def partition(self, nums, left, right):
        pivotvalue = nums[right]
        i = left
        for j in range(left,right+1):
            if nums[j]<pivotvalue:
                nums[i],nums[j]=nums[j],nums[i]
                i+=1
        nums[i],nums[right]=nums[right],nums[i]
        return i
    def select(self, nums, left, right, k):
        if left == right:
            return nums[left]
        pivot = self.partition(nums, left, right)
        if k == pivot:
            return nums[k]
        elif k < pivot:
            return self.select(nums, left, pivot - 1, k)
        else:
            return self.select(nums, pivot + 1, right, k)
```
