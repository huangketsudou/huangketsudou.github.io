---
title: leetcode周赛188
date: 2020-05-10 11:54:40
tags: 
    - leetcode
    - 算法周赛
    - python 
categories:
    - 程序人生
    - 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?6)
<!-- more -->


### 题目1：[5404. 用栈操作构建数组](https://leetcode-cn.com/problems/build-an-array-with-stack-operations/)
给你一个目标数组 target 和一个整数 n。每次迭代，需要从  list = {1,2,3..., n} 中依序读取一个数字。

请使用下述操作来构建目标数组 target ：

Push：从 list 中读取一个新元素， 并将其推入数组中。
Pop：删除数组中的最后一个元素。
如果目标数组构建完成，就停止读取更多元素。
题目数据保证目标数组严格递增，并且只包含 1 到 n 之间的数字。

请返回构建目标数组所用的操作序列。


#### 解题思路
模拟栈的应用就可以

#### 代码

```python
class Solution:
    def buildArray(self, target: List[int], n: int) -> List[str]:
        k = 0
        ans = []
        m = len(target)
        for i in range(1, n + 1):
            ans.append('Push')
            if i != target[k]:
                ans.append('Pop')
            else:
                k += 1
                if k == m: break
        return ans
```


### 题目二：[5405. 形成两个异或相等数组的三元组数目](https://leetcode-cn.com/problems/count-triplets-that-can-form-two-arrays-of-equal-xor/)

给你一个整数数组 arr 。

现需要从数组中取三个下标 i、j 和 k ，其中 (0 <= i < j <= k < arr.length) 。

a 和 b 定义如下：

a = arr\[i] ^ arr\[i + 1] ^ ... ^ arr\[j - 1]
b = arr\[j] ^ arr\[j + 1] ^ ... ^ arr\[k]
注意：^ 表示 按位异或 操作。

请返回能够令 a == b 成立的三元组 (i, j , k) 的数目。

#### 解题思路
O(N^3)的思路是三层循环
O(N^2)的思路是对于a==b,那么a^b ==0,所以我们的目标就是找到i,k范围内异或和为0的结果就可以了
那么i，k范围内就有k-i个方案符合要求


#### 代码：

```python
#O(N^2)
class Solution:
    def countTriplets(self, arr: List[int]) -> int:
        if len(arr)<2:
            return 0
        ans=0
        for i in range(len(arr)):
            temp=arr[i]
            for j in range(i+1,len(arr)):
                temp=temp^arr[j]
                if temp==0:
                    ans+=j-i
        return ans

```

```python
#O(N^3)
class Solution:
    def countTriplets(self, arr: List[int]) -> int:
        cumxor = [0]
        n = len(arr)
        ans=0
        for i in range(n):
            cumxor.append(cumxor[i] ^ arr[i])
        for i in range(1, n):
            for j in range(i + 1, n + 1):
                for k in range(j, n + 1):
                    a = cumxor[j - 1] ^ cumxor[i - 1]
                    b = cumxor[k] ^ cumxor[j - 1]
                    if a==b:
                        ans+=1
        return ans
```


### 题目3：[5406. 收集树上所有苹果的最少时间](https://leetcode-cn.com/problems/minimum-time-to-collect-all-apples-in-a-tree/)

给你一棵有 n 个节点的无向树，节点编号为 0 到 n-1 ，它们中有一些节点有苹果。通过树上的一条边，需要花费 1 秒钟。你从 节点 0 出发，请你返回最少需要多少秒，可以收集到所有苹果，并回到节点 0 。

无向树的边由 edges 给出，其中 edges\[i] = \[fromi, toi] ，表示有一条边连接 from 和 toi 。除此以外，还有一个布尔数组 hasApple ，其中 hasApple[i] = true 代表节点 i 有一个苹果，否则，节点 i 没有苹果。

#### 解题思路


#### 代码
dfs算法，设置一个额外的self.ans记录摘苹果走的路
算法返回该路径上是否存在苹果，时返回true，否则放回false

```python
from typing import List
from collections import defaultdict, deque


class Solution:
    def __init__(self):
        self.ans=0
    def minTime(self, n: int, edges: List[List[int]], hasApple: List[bool]) -> int:
        tree = defaultdict(list)
        for start, end in edges:
            tree[start].append(end)

        def dfs(node):
            if len(tree[node]) == 0 and not hasApple[node]:
                return False
            elif len(tree[node]) == 0 and hasApple[node]:
                return True
            had = hasApple[node]
            for i in tree[node]:
                child = dfs(i)
                had |= child
                if child:
                    self.ans+=2
            return had
        dfs(0)
        return self.ans


k = Solution()
print(k.minTime(n = 7, edges = [[0,1],[0,2],[1,4],[1,5],[2,3],[2,6]], hasApple = [False,False,True,False,False,True,False]))
```


### 题目四：[5407. 切披萨的方案数](https://leetcode-cn.com/problems/number-of-ways-of-cutting-a-pizza/)

给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。
切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。
请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

#### 解题思路
dfs进行求解，注意在切每一刀时，两者是相互独立的，
这道题最关键的地方在于如何在切割时快速求出这种切法满足至少有一个苹果的条件
因此代码中的remain数组用来记录从i，j开始的右下角的苹果的数量利用remian\[i]\[j]-remain\[i+1]\[j],就可以快速求解


#### 代码

```python
class Solution:
    def ways(self, pizza: List[str], k: int) -> int:
        remain = [[0 for j in range(len(pizza[0]) + 1)] for i in range(len(pizza) + 1)]
        for i in range(len(pizza) - 1, -1, -1):
            for j in range(len(pizza[0]) - 1, -1, -1):
                remain[i][j] = remain[i + 1][j] + remain[i][j + 1] - remain[i + 1][j + 1] + int(pizza[i][j] == 'A')
                # remain[i][j]表示i，j构成的右下角的矩形有多少苹果
        print(remain)
        mod = int(1e9 + 7)

        @functools.lru_cache(None)
        def way(x, y, kk):
            if remain[x][y] == 0:
                return 0
            if kk == 0:
                return 1
            ans = 0
            for xx in range(x + 1, len(pizza)):
                if remain[x][y] - remain[xx][y] > 0:
                    ans += way(xx, y, kk - 1)
                    ans %= mod
            for yy in range(y + 1, len(pizza[0])):
                if remain[x][y] - remain[x][yy] > 0:
                    ans += way(x, yy, kk - 1)
                    ans %= mod
            return ans

        return way(0, 0, k - 1)
```