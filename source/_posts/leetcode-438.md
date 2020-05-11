---
title: leetcode-438
date: 2020-05-11 13:21:14
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

![图片](http://api.mtyqx.cn/api/random.php?45)
<!-- more -->


### 题目[leetcode-438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

给定一个字符串 s 和一个非空字符串 p，找到 s 中所有是 p 的字母异位词的子串，返回这些子串的起始索引。
字符串只包含小写英文字母，并且字符串 s 和 p 的长度都不超过 20100。

说明：
字母异位词指字母相同，但排列不同的字符串。
不考虑答案输出的顺序。


#### 用例
输入:
s: "cbaebabacd" p: "abc"

输出:\[0, 6\]

输入:
s: "abab" p: "ab"

输出:\[0, 1, 2\]
#### 解题思路
滑动窗口

#### 代码

```python
from collections import Counter
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        res = []
        n=len(p)
        m=len(s)
        sCounter = Counter(s[:n-1])
        pCounter = Counter(p)
        for i in range(n-1, m):
            sCounter[s[i]] += 1
            if sCounter == pCounter:
                res.append(i-n+1)
            sCounter[s[i-n+1]] -= 1
            if sCounter[s[i-n+1]] == 0:
                del sCounter[s[i-n+1]]
        return res
```