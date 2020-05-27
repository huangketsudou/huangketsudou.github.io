---
title: leetcode-day13
date: 2020-05-27 10:51:33
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

![图片](http://api.mtyqx.cn/api/random.php?617)
<!-- more -->

### 题目[974.和可被 K 整除的子数组](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/)
给定一个整数数组 A，返回其中元素之和可被 K 整除的（连续、非空）子数组的数目。

#### 用例
输入：A = \[4,5,0,-2,-3,1], K = 5
输出：7
#### 解题思路
利用前缀和以及哈希表，实现在O(1)时间内查找，两个数同余，那么这两个数的差就能被其除数整除
，注意不同的编程语言对于负数求余有不同的表现

#### 代码
```python
from typing import List
from collections import defaultdict


class Solution:
    def subarraysDivByK(self, A: List[int], K: int) -> int:
        n = len(A)
        if not n:
            return 0
        dictionary = defaultdict(int)
        dictionary[0] = 1
        prev = 0
        ans = 0
        for i in range(n):
            cur = prev + A[i]
            ans += dictionary[cur % K]
            ans += dictionary[K - cur % K]
            dictionary[cur % K] += 1
            prev = cur
        return ans

class Solution:
    def subarraysDivByK(self, A: List[int], K: int) -> int:
        record = {0: 1}
        total = 0
        for elem in A:
            total += elem
            modulus = total % K
            record[modulus] = record.get(modulus, 0) + 1

        ans = 0
        for x, cx in record.items():
            ans += cx * (cx - 1) // 2
        return ans
```

### 题目[508.出现次数最多的子树元素和](https://leetcode-cn.com/problems/most-frequent-subtree-sum/)
给你一个二叉树的根结点，请你找出出现次数最多的子树元素和。一个结点的「子树元素和」定义为以该结点为根的二叉树上所有结点的元素之和（包括结点本身）。

你需要返回出现次数最多的子树元素和。如果有多个元素出现的次数相同，返回所有出现次数最多的子树元素和（不限顺序）。

#### 解题思路
dfs深度遍历，用dict记录相应出现的值
#### 代码
```python
from collections import defaultdict


class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Solution:
    def findFrequentTreeSum(self, root: TreeNode) -> List[int]:
        self.dictionary = defaultdict(int)
        self.maxtime = 0

        def dfs(node):
            if not node:
                return 0
            left = dfs(node.left)
            right =dfs(node.right)
            summary = node.val + left + right
            self.dictionary[summary] += 1
            if self.dictionary[summary] > self.maxtime:
                self.maxtime = self.dictionary[summary]
            return summary
        dfs(root)
        ans=[]
        for key,values in self.dictionary.items():
            if values == self.maxtime:
                ans.append(key)
        return ans
```

### 题目[513.找树左下角的值](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)
给定一个二叉树，在树的最后一行找到最左边的值。

#### 解题思路
层序遍历
#### 代码
```python
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findBottomLeftValue(self, root: TreeNode) -> int:
        target = -1
        stack = deque()
        stack.append(root)
        while stack:
            node = stack.popleft()
            target = node.val
            if node.right:
                stack.append(node.right)
            if node.left:
                stack.append(node.left)
        return target
```