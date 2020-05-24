---
title: leetcode周赛190
date: 2020-05-24 14:01:03
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

![图片](http://api.mtyqx.cn/api/random.php?8421)
<!-- more -->

### 题目[5416.检查单词是否为句中其他单词的前缀](https://leetcode-cn.com/problems/check-if-a-word-occurs-as-a-prefix-of-any-word-in-a-sentence/)
给你一个字符串 sentence 作为句子并指定检索词为 searchWord ，其中句子由若干用 单个空格 分隔的单词组成。
请你检查检索词 searchWord 是否为句子 sentence 中任意单词的前缀。

1. 如果 searchWord 是某一个单词的前缀，则返回句子 sentence 中该单词所对应的下标（下标从 1 开始）。
2. 如果 searchWord 是多个单词的前缀，则返回匹配的第一个单词的下标（最小下标）。
3. 如果 searchWord 不是任何单词的前缀，则返回 -1 。


#### 用例
输入：sentence = "i love eating burger", searchWord = "burg"
输出：4

输入：sentence = "this problem is an easy problem", searchWord = "pro"
输出：2

输入：sentence = "i am tired", searchWord = "you"
输出：-1

#### 解题思路
利用split分割，然后找出来就可以，注意string.index会返回错误，string.find会返回-1
#### 代码
```python
from collections import Counter


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def __init__(self):
        self.ans = 0

    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        def dfs(node, tmp):
            if not node.left and not node.right:
                count = set()
                for num in tmp:
                    if num not in count:
                        count.add(num)
                    else:
                        count.remove(num)
                if len(count) <= 1:
                    self.ans += 1
                return
            if node.left:
                dfs(node.left, tmp + [node.val])
            if node.right:
                dfs(node.right, tmp + [node.val])

        dfs(root, [root.val])
        return self.ans
```

### 题目[5417.定长子串中元音的最大数目](https://leetcode-cn.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

给你字符串 s 和整数 k 。
请返回字符串 s 中长度为 k 的单个子字符串中可能包含的最大元音字母数。
英文中的 元音字母 为（a, e, i, o, u）。

#### 用例
输入：s = "abciiidef", k = 3
输出：3

输入：s = "aeiou", k = 2
输出：2

输入：s = "leetcode", k = 3
输出：2

输入：s = "rhythms", k = 4
输出：0

#### 解题思路
双指针法计数
#### 代码
```python
class Solution:
    def maxVowels(self, s: str, k: int) -> int:
        n = len(s)
        cur = 0
        for i in range(k):
            if s[i] in 'aiueo':
                cur += 1
        ans = cur
        r = k - 1
        l = 0
        while r < n - 1:
            if s[l] in 'aiueo':
                cur -= 1
            l += 1
            r += 1
            if s[r] in 'aiueo':
                cur += 1
            ans = max(ans, cur)
        return ans
```

### 题目[5418.二叉树中的伪回文路径](https://leetcode-cn.com/problems/pseudo-palindromic-paths-in-a-binary-tree/)
给你一棵二叉树，每个节点的值为 1 到 9 。我们称二叉树中的一条路径是 「伪回文」的，当它满足：路径经过的所有节点值的排列中，存在一个回文序列。
请你返回从根到叶子节点的所有路径中 伪回文 路径的数目。


#### 用例
输入：root = \[2,3,1,3,1,null,1]
输出：2 

#### 解题思路
dfs算法，并用一个tmp记录相应的路线，最后对tmp里面的数字进行分析，要求所有的数都出现偶数次，或者仅有一个出现了奇数次
* 优化
    - 上面的方法每次需要O(K)的时间来判断是否为回文，K为tmp的长度，可以优化到O(1)，因为其中的数字为1到9，所有可以用一个掩码记录每个数字，出现的次数
    ，当出现奇数次，对应的位上为1，偶数次为0,可以用异或解决,快速判断数字二进制中仅有一个1用`n&(n-1)`
#### 代码
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def __init__(self):
        self.ans = 0

    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        def dfs(node, tmp):
            if not node.left and not node.right:
                count = set()
                for num in tmp:
                    if num not in count:
                        count.add(num)
                    else:
                        count.remove(num)
                if len(count) <= 1:
                    self.ans += 1
                return
            if node.left:
                dfs(node.left, tmp + [node.val])
            if node.right:
                dfs(node.right, tmp + [node.val])

        dfs(root, [root.val])
        return self.ans
```

```python
class Solution:
    def __init__(self):
        self.ans = 0

    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        def dfs(node, tmp):
            if not node.left and not node.right:
                if tmp == 0 or tmp & (tmp - 1) == 0:
                    self.ans += 1
                return
            if node.left:
                dfs(node.left, tmp ^ (1 << node.left.val))
            if node.right:
                dfs(node.right, tmp ^ (1 << node.right.val))

        dfs(root, 1 << root.val)
        return self.ans
```


### 题目[5419.两个子序列的最大点积](https://leetcode-cn.com/problems/max-dot-product-of-two-subsequences/)
给你两个数组 nums1 和 nums2 。
请你返回 nums1 和 nums2 中两个长度相同的 非空 子序列的最大点积。
数组的非空子序列是通过删除原数组中某些元素（可能一个也不删除）后剩余数字组成的序列，但不能改变数字间相对顺序。比方说，\[2,3,5] 是 \[1,2,3,4,5] 的一个子序列而 \[1,5,3] 不是。

#### 用例
输入：nums1 = \[2,1,-2,5], nums2 = \[3,0,-6]
输出：18

输入：nums1 = \[3,-2], nums2 = \[2,-6,7]
输出：21

#### 解题思路
经典的动态规划题目，dp\[i]\[j]表示第一个数组取i长度，第二个数组取j长度时可以构成的最大的点乘积，此时的状态转移方程为：
`dp[i][j]=max(dp[i][j-1],dp[i-1][j],nums1[i]*nums2[j]+dp[i-1][j-1],nums1[i]*nums2[j]`,
#### 代码

```python
class Solution:
    def maxDotProduct(self, nums1: List[int], nums2: List[int]) -> int:
        n=len(nums1)
        m=len(nums2)
        dp = [[float('-inf')]*(m+1) for _ in range(n+1)]
        for i in range(1, n+1):
            for j in range(1, m+1):
                dp[i][j] = max(nums1[i-1]*nums2[j-1]+dp[i-1][j-1], dp[i-1][j], dp[i][j-1],nums1[i-1]*nums2[j-1])
        print(dp)
        return dp[n][m]
```