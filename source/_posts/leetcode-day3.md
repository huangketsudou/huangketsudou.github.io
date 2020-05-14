---
title: leetcode-day3
date: 2020-05-14 10:37:34
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

![图片](http://api.mtyqx.cn/api/random.php?1)
<!-- more -->


### 题目136[只出现一次的数字](https://leetcode-cn.com/problems/single-number/)
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

#### 用例
输入: \[2,2,1]
输出: 1

输入: \[4,1,2,1,2]
输出: 4


#### 解题思路
1. 哈希表计数
2. 对数组内元素求和，利用set记录不同的数，2倍set的和减去数组和即为所求
3. 利用异或性质 x^x=0

#### 代码

```python
from functools import reduce
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        #return reduce(lambda x,y:x^y,nums)
    
        ans=0
        for i in nums:
            ans^=i
        return ans
        
from collections import Counter
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        datas = Counter(nums)
        for each in datas:
            if datas[each] == 1: return each

class Solution(object):
    def singleNumber(self, nums):
        return 2 * sum(set(nums)) - sum(nums)
```


类似的题目
### 137[只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

#### 代码
```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        ones, twos = 0, 0
        for i in nums:
            ones = ones ^ i & ~ twos
            twos = twos ^ i & ~ ones
        return ones
```

### 260[只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/)
给定一个整数数组 nums，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。
#### 代码
```python
class Solution:
    def singleNumber(self, nums: List[int]) -> List[int]:
        bitmask = 0
        for num in nums:
            bitmask ^= num

        # rightmost 1-bit diff between x and y
        diff = bitmask & (-bitmask)
        #bitmask最右边的1要么来自x，要么来自y，x & （-x）的操作可以保留最右边的1
        #通过diff标记可以找到其中一个，另一个就是bitmask ^ x

        x = 0
        for num in nums:
            # bitmask which will contain only x
            if num & diff:
                x ^= num

        return [x, bitmask ^ x]
```
### 268[缺失数字](https://leetcode-cn.com/problems/missing-number/)
给定一个包含 0, 1, 2, ..., n 中 n 个数的序列，找出 0 .. n 中没有出现在序列中的那个数。
#### 代码
```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        flag=nums[0]
        for num in nums[1:]:
            flag ^= num

        for i in range(len(nums)+1):
            flag ^= i

        return flag
```
### 287[寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)
给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

说明：
1. 不能更改原数组（假设数组是只读的）。
2. 只能使用额外的 O(1) 的空间。
3. 时间复杂度小于 O(n2) 。
4. 数组中只有一个重复的数字，但它可能不止重复出现一次。

#### 解题思路
利用排序或者哈希表都可以实现，代码给出利用快慢指针的查找方法

```python
class Solution:
    def findDuplicate(self, nums: List[int]) -> int:
        tortoise = nums[0]
        hare = nums[0]
        while True:
            tortoise = nums[tortoise]
            hare = nums[nums[hare]]
            if tortoise == hare:
                break

        # Find the "entrance" to the cycle.
        ptr1 = nums[0]
        ptr2 = tortoise
        while ptr1 != ptr2:
            ptr1 = nums[ptr1]
            ptr2 = nums[ptr2]

        return ptr1
```

### 454[四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)
给定四个包含整数的数组列表 A , B , C , D ,计算有多少个元组 (i, j, k, l) ，使得 A[i] + B[j] + C[k] + D[l] = 0。
为了使问题简单化，所有的 A, B, C, D 具有相同的长度 N，且 0 ≤ N ≤ 500 。所有整数的范围在 -228 到 228 - 1 之间，最终结果不会超过 231 - 1 。

#### 用例
输入:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

输出:
2

#### 解题思路
哈希表记录两数组的求和结果，记录出现的次数

#### 代码
```python
class Solution:
    def fourSumCount(self, A: List[int], B: List[int], C: List[int], D: List[int]) -> int:
        target = defaultdict(int)
        for i in C:
            for j in D:
                target[i + j] += 1
        ans=0
        for m in A:
            for n in B:
                ans+=target[-(m+n)]
        return ans
```



### 132[312模式](https://leetcode-cn.com/problems/132-pattern/)
给定一个整数序列：a1, a2, ..., an，一个132模式的子序列 ai, aj, ak 被定义为：当 i < j < k 时，ai < ak < aj。设计一个算法，当给定有 n 个数字的序列时，验证这个序列中是否含有132模式的子序列。
注意：n 的值小于15000。

#### 用例
输入: \[1, 2, 3, 4]

输出: False

输入: [3, 1, 4, 2]

输出: True

#### 解题思路
从后向前维护一个单调递减的栈


### 代码

```python
class Solution:
    def find132pattern(self, nums: List[int]) -> bool:
        ak = float("-inf")
        stack = []
        for num in reversed(nums):
            if ak > num: return True
            while stack and stack[-1] < num:
                ak = stack.pop()
            stack.append(num)
        return False
```
