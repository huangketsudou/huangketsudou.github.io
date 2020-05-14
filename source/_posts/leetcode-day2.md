---
title: leetcode-day2
date: 2020-05-13 19:58:21
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

![图片](http://api.mtyqx.cn/api/random.php?39)
<!-- more -->


###179[最大数](https://leetcode-cn.com/problems/largest-number/)
给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

#### 用例

输入: \[10,2]
输出: 210

输入: \[3,30,34,5,9]
输出: 9534330

#### 解题思路
我们可以将数组中的数字转换为字符串，对于两个字符串x，y组合起来的数字，比较
x+y与y+x的大小，并对两者进行排序，大的在前如'3'+'30'>'30'+'3',返回最后结果时注意全为0的情况
此外还可以用传统的dfs方法，开销很大


#### 代码

```python
class Solution:
    def largestNumber(self, nums: List[int]) -> str:
        import functools
        def cmp(x,y):
            if x+y>y+x:
                return 1
            elif x+y<y+x:
                return -1
            else:
                return 0

        nums=list(map(str,nums))
        nums.sort(key=functools.cmp_to_key(cmp),reverse=True)
        answer=''.join(nums)
        return '0' if answer[0]=='0' else answer
```

###102[二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。

#### 解题思路
二叉树的遍历算法，下面给出各种遍历算法的常用代码

#### 代码

```python
class Solution:

    def inorderTravelRecur(self,root:TreeNode):
        #递归实现中序遍历

        def core(root,answer):
            if not root:return
            core(root.left,answer)
            answer.append(root.val)
            core(root.right,answer)

        answer = []
        core(root,answer)
        return answer


    def leveltravelStack(self,root:TreeNode):
        #栈实现层遍历
        if not root:return []
        answer=[]
        stack=[root]
        while stack:
            tmp=stack.pop(0)
            if tmp.left:
                stack.append(tmp.left)
            if tmp.right:
                stack.append(tmp.right)
            answer.append(tmp.val)
        return answer


    def preTravelRecur(self,root):
        #递归实现前序遍历
        def core(root,answer):
            if not root:
                return
            answer.append(root.val)
            core(root.left,answer)
            core(root.right,answer)

        answer=[]
        core(root,answer)
        return answer


    def postTravelRecur(self,root):
        #递归实现后序遍历
        def core(root,answer):
            if not root:
                return
            core(root.left,answer)
            core(root.right,answer)
            answer.append(root.val)

        answer=[]
        core(root,answer)
        return answer


    def inorderTravelStack(self,root):
        #栈实现中序遍历
        answer=[]
        stack=[]
        while root or stack:
            while root:
                stack.append(root)
                root=root.left
            root=stack.pop(-1)
            answer.append(root.val)
            root=root.right
        return answer


    def preTravelStack(self,root):
        #栈实现前序遍历
        answer=[]
        stack=[]
        while root or stack:
            while root:
                stack.append(root)
                answer.append(root.val)
                root=root.left
            root=stack.pop(-1)
            root=root.right
        return answer


    def postTravelStack(self,root):
        #利用前序遍历与后序遍历的关系实现栈后序遍历
        #前序遍历：根左右，后序遍历：左右根
        #因此利用前序遍历改变遍历顺序为根右左，再倒序输出既能得到
        answer=[]
        stack=[]
        stack2=[]
        while root or stack:
            while root:
                stack.append(root)
                stack2.append(root)
                root=root.right
            root=stack.pop(-1)
            root=root.left
        while stack2:
            answer.append(stack2.pop(-1).val)
        return answer
```

### 451[根据字符出现频率排序](https://leetcode-cn.com/problems/sort-characters-by-frequency/)

给定一个字符串，请将字符串里的字符按照出现的频率降序排列。

#### 用例

输入:
"tree"

输出:
"eert"

输入:
"cccaaa"

输出:
"cccaaa"

#### 解题思路
利用哈希表存储再排序就可以了


#### 代码

```python
from collections import Counter
class Solution:
    def frequencySort(self, s: str) -> str:
        c = Counter(s)
        item=list(c.items())
        item.sort(key=lambda x:x[1],reverse=True)
        ans=''
        for k,v in item:
            ans+=k*v
        return ans
```


### 452[用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)
在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球
直径的开始和结束坐标。由于它是水平的，所以y坐标并不重要，因此只要知道开始
和结束的x坐标就足够了。开始坐标总是小于结束坐标。平面内最多存在104个气球。

一支弓箭可以沿着x轴从不同点完全垂直地射出。在坐标x处射出一支箭，若有一个
气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，
则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限
地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

#### 用例

输入:
[[10,16], [2,8], [1,6], [7,12]]

输出:
2

#### 解题思想
可以利用动态规划思想或者对数组进行排序，比较end与start，发现重合就不计数，否则计数加一
，这道题和leetcode-435时一样的
#### 代码

```python
from typing import List


class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        points.sort(key=lambda x: x[1])
        prev = float('-inf')
        ans = 0
        for start, end in points:
            if start > prev:
                ans+=1
                prev=end
        return ans
```