---
title: leetcode-day12
date: 2020-05-25 16:00:56
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

![图片](http://api.mtyqx.cn/api/random.php?888)
<!-- more -->

### 题目[503.下一个更大元素 II](https://leetcode-cn.com/problems/next-greater-element-ii/)
给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。


#### 用例
输入: \[1,2,1]

输出: \[2,-1,2]

输入: \[1,2,3,4,5,6,7,8,9,10,9,8,7,6,5,4,3,2,1]

输出: \[2,3,4,5,6,7,8,9,10,-1,10,9,8,7,6,5,4,3,2]
#### 解题思路
利用单调栈，并遍历两次可得结果

#### 代码
```python
from collections import deque

class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n = len(nums)
        if not n:
            return []
        stack = deque()
        nums = nums + nums
        ans = [-1] * n * 2
        for i, num in enumerate(nums):
            while len(stack) and nums[stack[-1]] < num:
                idx = stack.pop()
                ans[idx] = num
            stack.append(i)
        return ans[:len(ans) // 2]
```

### 题目[146.LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)
运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
写入数据 put(key, value) - 如果密钥已经存在，则变更其数据值；如果密钥不存在，则插入该组「密钥/数据值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。


#### 用例
无
#### 解题思路
1. 利用双向链表实现
2. 利用python中的orderdict实现

#### 代码
```python
from typing import List
import functools


class Node:
    def __init__(self):
        self.key = 0
        self.val = 0
        self.next = None
        self.prev = None


class LRUCache:

    def __init__(self, capacity: int):
        self._cache = {}
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head
        self.capacity = capacity
        self.size = 0

    def __removenode(self, node):
        prev = node.prev
        next = node.next
        prev.next = next
        next.prev = prev

    def __addnode(self, node):
        hnext = self.head.next
        self.head.next = node
        node.prev = self.head
        node.next = hnext
        hnext.prev = node

    def __movetohead(self, node):
        self.__removenode(node)
        self.__addnode(node)

    def __poptail(self):
        node = self.tail.prev
        self.__removenode(node)
        return node

    def get(self, key: int) -> int:
        node = self._cache.get(key, None)
        if not node: return -1
        self.__movetohead(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        node = self._cache.get(key)
        if not node:
            newnode = Node()
            newnode.key = key
            newnode.val = value
            self._cache[key] = newnode
            self.__addnode(newnode)
            self.size += 1
            if self.size > self.capacity:
                tail=self.__poptail()
                del self._cache[tail.key]
                self.size-=1
        else:
            node.val=value
            self.__movetohead(node)




from collections import OrderedDict

class LRUCache2(OrderedDict):

    def __init__(self,capacity):
        self.capacity=capacity

    def get(self, key):
        if key not in self:
            return -1
        self.move_to_end(key)
        return self[key]


    def put(self,key,value):
        if key in self:
            self.move_to_end(key)
        self[key]=value
        if len(self)>self.capacity:
            self.popitem(last=False)
```

### 题目[]()
给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。

请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。
你可以假设 nums1 和 nums2 不会同时为空。

#### 用例
nums1 = \[1, 3]
nums2 = \[2]

则中位数是 2.0


nums1 = \[1, 2]
nums2 = \[3, 4]

则中位数是 (2 + 3)/2 = 2.5
#### 解题思路
划分数字，利用中位数左边所有的数小于右边的数实现划分，参考这篇[题解](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-s-114/)



#### 代码
```python
class Solution:
    def findMedianSortedArrays(self,nums1,nums2):
        m,n=len(nums1),len(nums2)
        if m>n:#保证i计算出的j一定落在可取的范围内
            nums1,nums2,m,n=nums2,nums1,n,m
        if n==0:#证明两个都为空
            return 0
        imin,imax,half=0,m,(m+n+1)//2
        while imin<=imax:
            i=(imin+imax)//2
            j=half-i
            if i<m and nums2[j-1]>nums1[i]:
                imin=i+1
            elif i>0 and nums1[i-1]>nums2[j]:
                imax=i-1
            else:
                if i==0:#nums1的最小值大于nums2的最大值
                    maxleft=nums2[j-1]
                elif j==0:#m==n时，成立
                    maxleft=nums1[i-1]
                else:
                    maxleft=max(nums1[i-1],nums2[j-1])

                if (m+n)%2==1:
                    return maxleft

                if i==m:#nums1的最大值小于nums2的最小值
                    minright=nums2[j]
                elif j==n:#m==n时成立
                    minright=nums1[i]
                else:
                    minright=min(nums1[i],nums2[j])

                return (maxleft+minright)/2.0



k=Solution()
print(k.findMedianSortedArrays([1,3],[]))
class Solution:
    def findMedianSortedArrays(self,nums1,nums2):
        m,n=len(nums1),len(nums2)
        if m>n:#保证i计算出的j一定落在可取的范围内
            nums1,nums2,m,n=nums2,nums1,n,m
        if n==0:#证明两个都为空
            return 0
        imin,imax,half=0,m,(m+n+1)//2
        while imin<=imax:
            i=(imin+imax)//2
            j=half-i
            if i<m and nums2[j-1]>nums1[i]:
                imin=i+1
            elif i>0 and nums1[i-1]>nums2[j]:
                imax=i-1
            else:
                if i==0:#nums1的最小值大于nums2的最大值
                    maxleft=nums2[j-1]
                elif j==0:#m==n时，成立
                    maxleft=nums1[i-1]
                else:
                    maxleft=max(nums1[i-1],nums2[j-1])

                if (m+n)%2==1:
                    return maxleft

                if i==m:#nums1的最大值小于nums2的最小值
                    minright=nums2[j]
                elif j==n:#m==n时成立
                    minright=nums1[i]
                else:
                    minright=min(nums1[i],nums2[j])

                return (maxleft+minright)/2.0



k=Solution()
print(k.findMedianSortedArrays([1,3],[]))
```