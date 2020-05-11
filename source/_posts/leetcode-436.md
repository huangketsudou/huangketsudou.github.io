---
title: leetcode-436
date: 2020-05-11 13:21:01
tags: 
- leetcode
- 算法
- python
categories: 
- 程序人生
- 算法
declare:
comments:
---

![图片](http://api.mtyqx.cn/api/random.php?38)
<!-- more -->

### 题目[leetcode-436寻找右区间](https://leetcode-cn.com/problems/find-right-interval/)

给定一组区间，对于每一个区间 i，检查是否存在一个区间 j，它的起始点大于或等于区间 i 的终点，这可以称为 j 在 i 的“右侧”。
对于任何区间，你需要存储的满足条件的区间 j 的最小索引，这意味着区间 j 有最小的起始点可以使其成为“右侧”区间。如果区间 j 不存在，则将区间 i 存储为 -1。最后，你需要输出一个值为存储的区间值的数组。

注意:
你可以假设区间的终点总是大于它的起始点。
你可以假定这些区间都不具有相同的起始点。


#### 解题思路
排序后利用二分法进行求解或者利用双指针

#### 代码
二分法

```python
class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        import bisect
        res = []
        loc = []
        for idx, val in enumerate(intervals):
            loc.append([val[0], idx])
        loc.sort()
        for _, right in intervals:
            l = bisect.bisect_left(loc, [right])
            if l >= len(loc):
                res.append(-1)
            else:
                res.append(loc[l][1])
        return res
```


双指针
```python
class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        n=len(intervals)
        res=[-1]*n
        intervals=[tuple(val) for val in intervals]
        loc={}
        for idx,val in enumerate(intervals):
            loc[val]=idx
        left_sort=sorted(intervals)
        right_sort=sorted(intervals,key=lambda x:x[1])
        i,j=0,0
        while i<n:
            while j<n and right_sort[j][1]<=left_sort[i][0]:
                res[loc[right_sort[j]]]=loc[left_sort[i]]
                j+=1
            i += 1
        return res
```