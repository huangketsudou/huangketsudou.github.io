---
title: leetcode-day14
date: 2020-05-28 11:28:11
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

![图片](http://api.mtyqx.cn/api/random.php?451)
<!-- more -->

### 题目[394.字符串解码](https://leetcode-cn.com/problems/decode-string/)
给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k\[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。
你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。
此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2\[4] 的输入。

#### 用例
s = "3\[a]2\[bc]", 返回 "aaabcbc".
s = "3\[a2\[c]]", 返回 "accaccacc".
s = "2\[abc]3\[cd]ef", 返回 "abcabccdcdcdef".

#### 解题思路
1. 调用辅助栈记录上一次的字符串以及需要的倍数
2. 利用递归解法

#### 代码
```python
import numpy as np
import pandas as pd
import copy
import heapq
from collections import Counter
from typing import List
from collections import deque, defaultdict
import matplotlib.pyplot as plt
from itertools import combinations, product
import time
import itertools
from functools import reduce
import collections
import math
import bisect


class Solution:
    def decodeString(self, s: str) -> str:
        stack = []
        res = ''
        multi = 0
        i = 0
        while i < len(s):
            if s[i].isdigit():
                multi = multi * 10 + int(s[i])
            elif s[i] == '[':
                stack.append([multi, res])
                res = ''
                multi = 0
            elif s[i] == ']':
                cmulti, lastres = stack.pop()
                res = lastres + cmulti * res
            else:
                res += s[i]
            i += 1
        return res


class Solution:
    def decodeString(self, s: str) -> str:
        n = len(s)
        i = 0
        multi = 0
        res = ''
        while i < n:
            if s[i].isdigit():
                multi = 10 * multi + int(s[i])
            elif s[i] == '[':
                lv = 1
                j = i
                while lv:
                    j += 1
                    if s[j] == '[':
                        lv += 1
                    elif s[j] == ']':
                        lv -= 1
                tmp = self.decodeString(s[i + 1:j])
                res = tmp * multi + res
                i = j
                multi = 0
            else:
                res += s[i]
            i += 1
        return res


class Solution:
    def decodeString(self, s: str) -> str:
        def dfs(s,i):
            multi = 0
            res = ''
            while i < len(s):
                if s[i].isdigit():
                    multi=10*multi+int(s[i])
                elif s[i] == '[':
                    i,tmp = dfs(s,i+1)
                    res += tmp*multi
                    multi = 0
                elif s[i] == ']':
                    return i,res
                else:
                    res += s[i]
                i += 1
            return res
        return dfs(s,0)
```
### [516.最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)
给定一个字符串s，找到其中最长的回文子序列，并返回该序列的长度。可以假设s的最大长度为1000。

#### 用例
输入："bbbab"
输出：4

输入："cbbd"
输出：2

#### 解题思路
动态规划或者递归，状态转移方程为
```python
if s[i]==s[j]:
    dp[i][j]=2+dp[i+1][j-1]
else:
    dp[i][j]=max(dp[i+1][j],dp[i][j-1])
```

#### 代码
```python
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        n = len(s)
        dp = [[0] * n for _ in range(n)]
        for i in range(n):
            dp[i][i] = 1
        for i in range(n - 1, -1, -1):
            for j in range(i + 1, n):
                if s[i] == s[j]:
                    dp[i][j] = 2 + dp[i + 1][j - 1]
                else:
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])
        return dp[0][-1] if n else 0


class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        import functools

        @functools.lru_cache(None)
        def dfs(i, j):
            if s[i:j + 1] == s[i:j + 1][::-1]: return j - i + 1
            if i > j: return 0
            res = 0
            if s[i] == s[j]:
                res = max(res, 2 + dfs(i + 1, j - 1))
            return max(res, dfs(i + 1, j), dfs(i, j - 1))

        return dfs(0, len(s) - 1)
```

### [515.在每个树行中找最大值](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/)
您需要在二叉树的每一行中找到最大的值。

#### 解题思路
层序遍历

#### 代码
```python
from typing import List
from collections import deque


class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Solution:
    def largestValues(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack = deque()
        stack.append(root)
        ans = []
        while stack:
            substack = deque()
            maximun = float('-inf')
            while stack:
                node = stack.popleft()
                maximun = max(maximun,node.val)
                if node.left:
                    substack.append(node.left)
                if node.right:
                    substack.append(node.right)
            stack = substack
            ans.append(maximun)
        return ans
```