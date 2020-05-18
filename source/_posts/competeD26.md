---
title: 第26场双周赛最后一题
date: 2020-05-18 20:47:40
tags:
- leetcode
- 算法
- python
- 动态规划
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->

### 题目分析要点
1. 本题目是一个完全背包问题，每个数字可以无限制使用
1. 数位中不会出现0，也就是不会有'011'这种不合法的数字
2. 总成本必须为target
3. 返回结果特别大，需要使用字符串来存数字，通过字符串比较数字大小的方法为：
    - 两字符串不等长，长的那个比较大
    - 两字符串等长，从高位向地位逐个比较，大的那个比较大

### 解题思路一
利用dfs遍历的方法在所有可能的结果中，直到数位的cost成本总和达到target

```
class Solution:
    #本代码来自赛后提交区用户@SQRPI
    def largestNumber(self, cost: List[int], tar: int) -> str:
        mi = min(cost)
        @lru_cache(None)
        def dp(target): # target 下的最大值
            if target == 0: return 0
            if target < mi: return -float('inf')
            res = -float('inf')
            for x in range(9):
                res = max(dp(target - cost[x])*10 + x + 1, res)
            return res
        res = dp(tar)
        return str(res) if res > 0 else "0"
```
但上面的代码是比较投机取巧的方式，利用python没有溢出的特性实现


### 解题思路二
采用动态规划的方式，我们定义一个dp数组，i为range(0,target+1),dp[i]为一个列表，里面保存了用来构成总成本i的数字，假设dp[k],k=0,1,2,..i-1的最优策略已经求出，那么对于dp[i]而言，他的最优策略就应该从dp[i-cost[j]]+[j+1],j=0,1,...,8中选取，对于某一个特定的j，假设dp[i-cost[j]]的最优策略构成为[x1,x2,..,xm]，则在其中插入j+1的位置是不确定的，但从要点分析中知，对于等长的字符串，数字字位大的在前的字符串数字较大，因此我们可以对其进行排序，然后进行比较

```
class Solution:
    def largestNumber(self, cost: List[int], target: int) -> str:
        dp = [[] for _ in range(target+1)]
        for i in range(1, target + 1):
            for j, c in enumerate(cost):
                if i == c:

                    dp[i]=self.cmp(dp[i],[j+1]).copy()
                elif i > c:
                    if len(dp[i - c]):
                        b = dp[i - c].copy()
                        b.append(j + 1)
                        dp[i] = self.cmp(dp[i], b).copy()
        for i in dp:
            print(i)
        return ''.join(map(str,dp[-1]))

    def cmp(self, a, b):
        a.sort(reverse=True)
        b.sort(reverse=True)
        if len(a) == len(b):
            return a if a > b else b
        elif len(a) < len(b):
            return b
        else:
            return a
```
但是上面的代码运行是很慢的，原因有两个
1. 频繁执行copy操作
2. 频繁执行列表的排序操作

### 解题思路三
上面的代码运行太慢，原因在于频繁的排序操作，回到要点分析，可以知道如果我们贪心的每次都选择最大值来构成可能的字符串，那么对于'777'这样的一个字符串，如果他的下一个可能的字位为'6',那么6插在'777'的最后才可以构成最大的数字，可以节省很多的排序操作。

```
class Solution:
    def largestNumber(self, cost: List[int], target: int) -> str:
        dp = [''] + [None] * target
        for i in range(8, -1, -1):
            for v in range(cost[i], target + 1):
                if dp[v - cost[i ]] is not None:
                    newval = dp[v - cost[i]]+str(i+1)
                    if dp[v] is None or self.cmp(newval,dp[v]):
                        dp[v] = newval
        return '0' if dp[target] is None else dp[target]

    def cmp(self,a,b):
        if len(a)>len(b):
            return True
        elif len(a)<len(b):
            return False
        else:
            for i in range(len(a)):
                if a[i]>b[i]:
                    return True
            return False
```