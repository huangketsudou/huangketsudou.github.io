---
title: leetcode双周赛27
date: 2020-06-01 15:29:14
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

![图片](http://api.mtyqx.cn/api/random.php?142)
<!-- more -->

### 题目[460.通过翻转子数组使两个数组相等](https://leetcode-cn.com/problems/make-two-arrays-equal-by-reversing-sub-arrays/)
给你两个长度相同的整数数组 target 和 arr 。
每一步中，你可以选择 arr 的任意 非空子数组 并将它翻转。你可以执行此过程任意次。
如果你能让 arr 变得与 target 相同，返回 True；否则，返回 False 。


#### 用例
```
输入：target = [1,2,3,4], arr = [2,4,1,3]
输出：true
```

```
输入：target = [7], arr = [7]
输出：true
```

#### 解题思路
排序后的列表相同就可以了

#### 代码
```python
class Solution:
    def canBeEqual(self, target: List[int], arr: List[int]) -> bool:
        return sorted(target) == sorted(arr)
```

### 题目[1461.检查一个字符串是否包含所有长度为 K 的二进制子串](https://leetcode-cn.com/problems/check-if-a-string-contains-all-binary-codes-of-size-k/)
给你一个二进制字符串 s 和一个整数 k 。
如果所有长度为 k 的二进制字符串都是 s 的子串，请返回 True ，否则请返回 False 。

#### 用例
输入：s = "00110110", k = 2
输出：true

输入：s = "00110", k = 2
输出：true
#### 解题思路
对字符串按照其二进制最大长度进行分割，并加入到set中，如果set的长度等于2**k,就满足

#### 代码
```python
class Solution:
    def hasAllCodes(self, s: str, k: int) -> bool:
        string = "{:0"+str(k)+"b}"
        for i in range(2**k):
            if string.format(i) not in s:
                return False
        return True
```

```python
class Solution(object):
    #一共有2**k种子串
    def hasAllCodes(self, s, k):
        """
        :type s: str
        :type k: int
        :rtype: bool
        """
        contains = set()
        N = len(s)
        for i in range(N - k + 1):
            contains.add(s[i:i + k])
        return len(contains) == (2 ** k)
```

### 题目[1462.课程安排 IV](https://leetcode-cn.com/problems/course-schedule-iv/)
你总共需要上 n 门课，课程编号依次为 0 到 n-1 。
有的课会有直接的先修课程，比如如果想上课程 0 ，你必须先上课程 1 ，那么会以 \[1,0] 数对的形式给出先修课程数对。

给你课程总数 n 和一个直接先修课程数对列表 prerequisite 和一个查询对列表 queries 。
对于每个查询对 queries\[i] ，请判断 queries\[i]\[0] 是否是 queries\[i]\[1] 的先修课程。
请返回一个布尔值列表，列表中每个元素依次分别对应 queries 每个查询对的判断结果。
注意：如果课程 a 是课程 b 的先修课程且课程 b 是课程 c 的先修课程，那么课程 a 也是课程 c 的先修课程。


#### 用例
输入：n = 2, prerequisites = \[\[1,0]], queries = \[\[0,1],\[1,0]]
输出：\[false,true]
#### 解题思路
1. 利用并查集可以做，但是需要利用递归将课程的前置的前置进行遍历
2. 采用dfs方法进行搜索
3. 利用动态规划，动态规划的思路为
    1. 把dp\[i]\[j]定义为i是j的前置课程
    2. 针对某一课程k检验i与j是否能通过k实现链接如果能，则有`dp[i][j]=dp[i][k] and dp[k][j]`
    3. 最后的状态转移方程为：`dp[i][k] = dp[i][k] or (dp[i][j] and dp[j][k])`
    

#### 代码
```python
from typing import List
from collections import defaultdict

class Solution:
    '''
    这段代码是错误的，因为没有考虑到prerequisites的顺序，如下的两个例子
    prerequisites = [[3, 4], [2, 3], [1, 2], [0, 1]]和
    prerequisites = [[0,1],[1,2],[2,3],[3,4]]，queries都一样，queries = [[0,4],[4,0],[1,3],[3,0]]
    第一个返回的的结果为[False,False,False,False]
    第二个返回的结果为[True,False,True,False],第二个返回结果才是对的，因为第一个prere没有正确的将所有的parent给到其子孙上
    '''
    def checkIfPrerequisite(self, n: int, prerequisites: List[List[int]], queries: List[List[int]]) -> List[bool]:
        mapping =defaultdict(set)
        for i in range(n):
            mapping[i].add(i)
        for prev,nxt in prerequisites:
            mapping[nxt].add(prev)
            mapping[nxt]|=mapping[prev]
        print(mapping)
        ans = []
        for prev,nxt in queries:
            if prev in mapping[nxt]:
                ans.append(True)
            else:
                ans.append(False)
        return ans


from functools import lru_cache
class Solution:
    #一定要用并查集的话用这个
    def checkIfPrerequisite(self, n: int, prerequisites: List[List[int]], queries: List[List[int]]) -> List[bool]:
        req = defaultdict(set)
        for L,R in prerequisites:
            req[L].add(R)
        #递归调用将所有的parent给出
        @lru_cache(None)
        def recur(L):
            for R in req[L]:
                recur(R)
            for R in list(req[L]):
                req[L]|=(req[R])

        for i in range(n):
            recur(i)

        return [R in req[L] for L,R in queries]


class Solution:
    def checkIfPrerequisite(self, n: int, prerequisites: List[List[int]], queries: List[List[int]]) -> List[bool]:
        g = [[False for j in range(n)] for i in range(n)]
        for u, v in prerequisites: #表示u是v的前置
            g[u][v] = True
        # i，j之间如果能通过k连接，那么i是j的前置
        # 注意必须先定好中间的节点k，再查看i，j能否通过k来联通
        # 如果先定好i，j，考虑一种情况，1 -> 2 -> 4 -> 3 ,那么检查1 ，3的时候无法通过一个节点正确连接，后面再检查2 3 之后也无法将1 3 连接起来
        # 但如果先检查所有能通过k连接的数据，那么能通过2连接1和4，之后再检查能通过4连接的，就能把1和3连接起来
        for k in range(n):
            for i in range(n):
                for j in range(n):
                    g[i][j] = g[i][j] or (g[i][k] and g[k][j])
        ans = []
        for u, v in queries:
            ans.append(g[u][v])
        return ans

class Solution(object):
    #dfs遍历法
    def checkIfPrerequisite(self, n, prerequisites, queries):
        """
        :type n: int
        :type prerequisites: List[List[int]]
        :type queries: List[List[int]]
        :rtype: List[bool]
        """
        self.graph = collections.defaultdict(list)
        for pre in prerequisites:
            self.graph[pre[0]].append(pre[1])
        return [self.dfs(query[0], query[1]) for query in queries]

    # start -> end ?
    @functools.lru_cache
    def dfs(self, start, end):
        if start == end:
            return True
        return any(self.dfs(nxt, end) for nxt in self.graph[start])


k=Solution()
print(k.checkIfPrerequisite(n = 5, prerequisites = [[3, 4], [2, 3], [1, 2], [0, 1]], queries = [[0,4],[4,0],[1,3],[3,0]]))
```

### 题目[1463.摘樱桃 II](https://leetcode-cn.com/problems/cherry-pickup-ii/)
给你一个 rows x cols 的矩阵 grid 来表示一块樱桃地。 grid 中每个格子的数字表示你能获得的樱桃数目。
你有两个机器人帮你收集樱桃，机器人 1 从左上角格子 (0,0) 出发，机器人 2 从右上角格子 (0, cols-1) 出发。

请你按照如下规则，返回两个机器人能收集的最多樱桃数目：

1. 从格子 (i,j) 出发，机器人可以移动到格子 (i+1, j-1)，(i+1, j) 或者 (i+1, j+1) 。
2. 当一个机器人经过某个格子时，它会把该格子内所有的樱桃都摘走，然后这个位置会变成空格子，即没有樱桃的格子。
3. 当两个机器人同时到达同一个格子时，它们中只有一个可以摘到樱桃。
4. 两个机器人在任意时刻都不能移动到 grid 外面。
5. 两个机器人最后都要到达 grid 最底下一行。


#### 用例
```
输入：grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
输出：24
```

```
输入：grid = [[1,0,0,0,0,0,1],[2,0,0,0,0,3,0],[2,0,9,0,0,0,0],[0,3,0,5,4,0,0],[1,0,2,3,0,0,6]]
输出：28
```

```
输入：grid = [[1,0,0,3],[0,0,0,3],[0,0,3,3],[9,0,3,3]]
输出：22
```
#### 解题思路
1. 动态规划
根据上一行的结果来选择下一行可能可行的结果，定义一个三维的dp数组`dp = [[[-1 for k in range(n)] for j in range(n)] for i in range(m)]`
,其中`dp[i][j][k]`表示在第i行两个机器人分别处在j，k位置时，能取到的最大值，注意当两个机器人走到同一个位置时，需要进去一个数

2. 递归
暴力搜索

#### 代码
```python
from typing import List


class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        m = len(grid)  # row
        n = len(grid[0])  # col
        dp = [[[-1 for k in range(n)] for j in range(n)] for i in range(m)]
        dp[0][0][n - 1] = grid[0][0] + grid[0][n - 1]
        for i in range(0, m - 1):
            for j in range(n):
                for k in range(n):
                    if dp[i][j][k] < 0:
                        continue
                    for jj in range(j - 1, j + 2):
                        if jj < 0 or jj >= n:
                            continue
                        for kk in range(k - 1, k + 2):
                            if kk < 0 or kk >= n:
                                continue
                            cur = dp[i][j][k] + grid[i + 1][jj] + grid[i + 1][kk]
                            if jj == kk:
                                cur -= grid[i + 1][jj]
                            if dp[i + 1][jj][kk] < 0 or cur > dp[i + 1][jj][kk]:
                                dp[i + 1][jj][kk] = cur
        ans = 0
        for j in range(n):
            for k in range(n):
                ans = max(ans, dp[-1][j][k])
        return ans


class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        R, C = len(grid), len(grid[0])

        @functools.lru_cache(None)
        def dp(r, c1, c2):
            if r == R - 1:
                return grid[r][c1] + grid[r][c2] if c1 != c2 else grid[r][c1]
            else:
                return (grid[r][c1] + grid[r][c2] if c1 != c2 else grid[r][c1]) + max(
                    dp(r + 1, a, b) for a in range(max(0, c1 - 1), min(C, c1 + 2)) for b in
                    range(max(0, c2 - 1), min(C, c2 + 2)))

        return dp(0, 0, C - 1)

```

