---
title: leetcode周赛203
date: 2020-08-23 14:16:23
tags:
- leetcode
- 算法
- python
- 周赛
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->


### 题目[5495. 圆形赛道上经过次数最多的扇区](https://leetcode-cn.com/problems/most-visited-sector-in-a-circular-track/)
给你一个整数 n 和一个整数数组 rounds 。有一条圆形赛道由 n 个扇区组成，扇区编号从 1 到 n 。现将在这条赛道上举办一场马拉松比赛，该马拉松全程由 m 个阶段组成。其中，第 i 个阶段将会从扇区 rounds[i - 1] 开始，到扇区 rounds[i] 结束。举例来说，第 1 阶段从 rounds[0] 开始，到 rounds[1] 结束。

请你以数组形式返回经过次数最多的那几个扇区，按扇区编号 升序 排列。

注意，赛道按扇区编号升序逆时针形成一个圆（请参见第一个示例）。

#### 用例
输入：n = 4, rounds = [1,3,1,2]
输出：[1,2]


输入：n = 2, rounds = [2,1,2,1,2,1,2,1,2]
输出：[2]

输入：n = 7, rounds = [1,3,5,7]
输出：[1,2,3,4,5,6,7]
#### 解题思路
思路一：模拟法 

思路二：题目并不在乎每一个经过了多少次，实际上最终的结果一定是[i,...j]这样的集合
所以只需要关注起点与终点就可以了

#### 代码

```python
from typing import List
from collections import defaultdict


class Solution:
    def mostVisited(self, n: int, rounds: List[int]) -> List[int]:
        visited = defaultdict(int)
        for i in range(1, len(rounds)):
            start = rounds[i - 1]
            end = rounds[i]
            while start != end:
                visited[start] += 1
                start = n if (start + 1) % n == 0 else (start + 1) % n
        visited[start] += 1
        M = max(visited.values())
        ans = []
        for k,v in visited.items():
            if v == M:
                ans.append(k)
        ans.sort()
        return ans


class Solution:
    def mostVisited(self, n: int, rounds: List[int]) -> List[int]:
        s, e = rounds[0], rounds[-1]
        if s <= e:
            # [起点, 终点]
            return list(range(s, e + 1))
        else:
            # [1, 终点]+[起点, n]
            return list(range(1, e + 1)) + list(range(s, n + 1))
```


### 题目[5496. 你可以获得的最大硬币数目](https://leetcode-cn.com/problems/maximum-number-of-coins-you-can-get/)
有 3n 堆数目不一的硬币，你和你的朋友们打算按以下方式分硬币：

每一轮中，你将会选出 任意 3 堆硬币（不一定连续）。
- Alice 将会取走硬币数量最多的那一堆。
- 你将会取走硬币数量第二多的那一堆。
- Bob 将会取走最后一堆。
- 重复这个过程，直到没有更多硬币。
- 给你一个整数数组 piles ，其中 piles[i] 是第 i 堆中硬币的数目。

返回你可以获得的最大硬币数目。


#### 用例
输入：piles = [2,4,1,2,7,8]
输出：9

输入：piles = [9,8,7,6,5,1,2,3,4]
输出：18
#### 解题思路
你在每次拿取的时候只能够拿到次大的，而bob只能拿到最小的，所以最后，bob拿数组中剩下最小的，
你拿次大的，alice拿最大的

#### 代码
```python
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        piles.sort()
        n = len(piles)
        time = n // 3
        idx = n - 2
        summary = 0
        while time != 0:
            summary+= piles[idx]
            idx-=2
            time -= 1
        return summary
```


### 题目[5497. 查找大小为 M 的最新分组](https://leetcode-cn.com/problems/find-latest-group-of-size-m/)
给你一个数组 arr ，该数组表示一个从 1 到 n 的数字排列。有一个长度为 n 的二进制字符串，该字符串上的所有位最初都设置为 0 。

在从 1 到 n 的每个步骤 i 中（假设二进制字符串和 arr 都是从 1 开始索引的情况下），二进制字符串上位于位置 arr[i] 的位将会设为 1 。

给你一个整数 m ，请你找出二进制字符串上存在长度为 m 的一组 1 的最后步骤。一组 1 是一个连续的、由 1 组成的子串，且左右两边不再有可以延伸的 1 。

返回存在长度 恰好 为 m 的 一组 1  的最后步骤。如果不存在这样的步骤，请返回 -1 。


#### 用例
输入：arr = [3,5,1,2,4], m = 1
输出：4

输入：arr = [3,1,5,4,2], m = 2
输出：-1

输入：arr = [1], m = 1
输出：1
#### 解题思路
思路一：用并查集将插入的结点连接起来，同时为了保证求取结果的快速，对连接后的长度进行保存，连接后原长度的数量减一，新增的长度数量加一

思路二：转换思维，插入1反向过来为从连续的1中切开，只要当前长度出现m，就立刻返回结果

#### 代码
```python
class Solution:

    def findLatestStep(self, arr: List[int], m: int) -> int:
        n = len(arr)
        self.dp = [-1] * (n + 2)
        self.count = defaultdict(int)
        self.number = defaultdict(int)
        last = -1
        for i, v in enumerate(arr):
            self.dp[v] = v
            self.count[v] = 1
            self.number[1] += 1
            if self.dp[v - 1] != -1:
                self.union(v, v - 1)
            if self.dp[v + 1] != -1:
                self.union(v + 1, v)
            # if m in set(self.count.values()):
            #     last = i + 1
            # for k in self.count.values():
            #     if k == m:
            #         last = i + 1
            #         break
            if self.number[m] != 0:
                last = i + 1
        return last

    def union(self, child, parent):
        parent = self.find(parent)
        child = self.find(child)
        self.dp[child] = parent
        self.number[self.count[parent]] -= 1
        self.count[parent] += self.count[child]
        self.number[self.count[parent]] += 1
        self.number[self.count[child]] -= 1
        del self.count[child]

    def find(self, x):
        if self.dp[x] != x:
            self.dp[x] = self.find(self.dp[x])
        return self.dp[x]
```

```java
import java.util.TreeSet;

class Solution {
    public int findLatestStep(int[] arr, int m) {
        TreeSet<Integer> set=new TreeSet<>();//记录插入点
        set.add(0);
        set.add(arr.length+1);
        if(arr.length==m) return arr.length;
        int n=arr.length;
        for (int i = n-1; i >=0; i--) {
            int index=arr[i];
            int a=set.lower(index);
            int b=set.higher(index);
            if(index-a-1==m||b-index-1==m){
                return i;
            }
            set.add(index);
        }
        return -1;
    }
}
```
### 题目[5498. 石子游戏 V](https://leetcode-cn.com/problems/stone-game-v/)
几块石子 排成一行 ，每块石子都有一个关联值，关联值为整数，由数组 stoneValue 给出。

游戏中的每一轮：Alice 会将这行石子分成两个 非空行（即，左侧行和右侧行）；Bob 负责计算每一行的值，即此行中所有石子的值的总和。Bob 会丢弃值最大的行，Alice 的得分为剩下那行的值（每轮累加）。如果两行的值相等，Bob 让 Alice 决定丢弃哪一行。下一轮从剩下的那一行开始。

只 剩下一块石子 时，游戏结束。Alice 的分数最初为 0 。

返回 Alice 能够获得的最大分数 。


#### 用例
输入：stoneValue = [6,2,3,4,5,5]
输出：18

输入：stoneValue = [7,7,7,7,7,7,7]
输出：28

输入：stoneValue = [4]
输出：0
#### 解题思路
一个简单的动态规划问题，定义dp[i][j]为当前仅剩下[i,j]之间的石头时，可以拿到的最大价值，遍历i，j之间的
结点作为分割点，取最大值即可

#### 代码
```python
class Solution:
    def stoneGameV(self, stoneValue: List[int]) -> int:
        n = len(stoneValue)
        dp = [[0] * n for _ in range(n)]
        summary = [0] * n
        summary[0] = stoneValue[0]
        for i in range(1, len(stoneValue)):
            summary[i] = stoneValue[i] + summary[i - 1]
        for i in range(n - 2, -1, -1):
            for j in range(i + 1, n):
                dp[i][j] = -1
                for k in range(i + 1, j + 1):
                    r = summary[j] - summary[k - 1]
                    l = summary[k - 1] - summary[i] + stoneValue[i]
                    if r > l:
                        dp[i][j] = max(dp[i][j], l + dp[i][k - 1])
                    elif r < l:
                        dp[i][j] = max(dp[i][j], r + dp[k][j])
                    else:
                        dp[i][j] = max(dp[i][j], r + max(dp[k][j], dp[i][k - 1]))
        return dp[0][n-1]
```



`要是做得再熟练一点就好了`