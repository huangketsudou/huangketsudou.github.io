---
title: leetcode-day9
date: 2020-05-21 10:34:13
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

![图片](http://api.mtyqx.cn/api/random.php?45)
<!-- more -->


### 题目5[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

#### 用例
输入: "babad"
输出: "bab"

输入: "cbbd"
输出: "bb"

#### 解题思路
1. 利用动态规划，二维动态规划，dp\[i]\[j]表示字符串从i到j是一个回文串(i<j)，那么对于一个定好尾节点为j的字符串，dp\[i]\[j]是回文串的条件是
s\[i]=s\[j],且dp\[i+1]\[j-1]也是回文串，注意特殊情况当j-i<3时，是不合法的，要单独说明

2. 中心扩展法，定好某一个字符作为回文的中心，向两边扩展，搜索最大的回文串长度

3. 马拉车算法——网上看分析

#### 代码
```python
class Solution:
    # 文章参考-小灰算法
    def longestPalindrome(self, s: str) -> str:
        s = '#' + '#'.join(list(s)) + '#'
        rightMax = 0
        MaxCenter = 0
        MaxLength = 0
        length = [0] * len(s)
        for i in range(1, len(s) - 1):
            if i >= rightMax:
                length[i] = self.find(s, i, 0)
                rightMax = MaxCenter + length[i]
                if length[i] > MaxLength:
                    MaxLength = length[i]
                    MaxCenter = i
            else:
                # 通过maxcenter找到右边的点i的最长回文长度
                LeftI = 2 * MaxCenter - i
                if i + length[LeftI] < rightMax:
                    length[i] = length[LeftI]
                else:
                    length[i] = self.find(s, i, length[LeftI])
                    rightMax = MaxCenter + length[i]
                    if length[i] > MaxLength:
                        MaxLength = length[i]
                        MaxCenter = i
        answer = s[MaxCenter - MaxLength:MaxCenter + MaxLength + 1]
        answer = answer.replace('#', '')
        return answer

    def find(self, s, center, length):
        while center - length - 1 >= 0 and center + length + 1 < len(s) and s[center - length - 1] == s[
            center + length + 1]:
            length += 1
        return length


class Solution:
    # 中心扩展法
    def longestPalindrome(self, s: str) -> str:
        if not s: return ''
        start = end = 0
        n = len(s)
        for i in range(len(s)):
            len1 = self.findlength(s, i, i, n)
            len2 = self.findlength(s, i, i + 1, n)
            length = max(len1, len2)
            if length > (end - start + 1):
                start = i - (length - 1) // 2
                end = i + length // 2
        return s[start:end + 1]

    def findlength(self, s, left, right, L):

        while left >= 0 and right < L and s[left] == s[right]:
            left -= 1
            right += 1
        return right - left - 1


class Solution:
    # 动态规划-转移方程dp[i][j]=dp[i+1][j-1] and s[i]==s[j]
    def longestPalindrome(self, s: str) -> str:
        if len(s) < 2: return s
        n = len(s)
        dp = [[0] * n for _ in range(n)]
        for i in range(n):
            dp[i][i] = 1
        maxlen = 1
        start = 0
        for j in range(1, n):
            for i in range(j):
                if s[i] == s[j]:
                    if j - i < 3:
                        dp[i][j] = 1
                    else:
                        dp[i][j] = dp[i + 1][j - 1]
                if dp[i][j]:
                    curlen = j - i + 1
                    if curlen > maxlen:
                        maxlen = curlen
                        start = i
        return s[start:start + maxlen]
```




### 题目[491.递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

给定一个整型数组, 你的任务是找到所有该数组的递增子序列，递增子序列的长度至少是2。
说明：
1. 给定数组的长度不会超过15。
2. 数组中的整数范围是 \[-100,100]。
3. 给定数组中可能包含重复数字，相等的数字应该被视为递增的一种情况。

#### 用例
```
输入: [4, 6, 7, 7]
输出: [[4, 6], [4, 7], [4, 6, 7], [4, 6, 7, 7], [6, 7], [6, 7, 7], [7,7], [4,7,7]]
```

#### 解题思路
利用dfs递归进行求解，注意数据中会有重复的数字，要求把这部分数字造成的重复序列去掉

第二个代码会更好些，因为在每次循环时通过将一个哈希表，将重复的序列直接排除了
#### 代码

```python
from typing import List


class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        if n < 2: return []
        ans=set()
        for i in range(n):
            self.dfs([nums[i]],ans,nums,i+1,n)
        return list(map(list,ans))


    def dfs(self,array,ans,nums,idx,L):

        for j in range(idx,L):
            if nums[j]>=array[-1]:
                array.append(nums[j])
                ans.add(tuple(array))
                self.dfs(array,ans,nums,j+1,L)
                array.pop()
        return


class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        res = []
        #用dict过滤掉重复的元素
        def _dfs(start, temp):
            dic = {}
            if len(temp) > 1:
                res.append(temp)

            for i in range(start, len(nums)):
                if dic.get(nums[i], 0):
                    continue

                if len(temp) == 0 or nums[i] >= temp[-1]:
                    dic[nums[i]] = 1
                    _dfs(i + 1, temp + [nums[i]])

        _dfs(0, [])
        return res

k=Solution()
print(k.findSubsequences([4,6,7,7]))
```



### 题目[486. 预测赢家](https://leetcode-cn.com/problems/predict-the-winner/)
给定一个表示分数的非负整数数组。 玩家1从数组任意一端拿取一个分数，随后玩家2继续从剩余数组任意一端拿取分数，然后玩家1拿，……。
每次一个玩家只能拿取一个分数，分数被拿取之后不再可取。直到没有剩余分数可取时游戏结束。最终获得分数总和最多的玩家获胜。

给定一个表示分数的数组，预测玩家1是否会成为赢家。你可以假设每个玩家的玩法都会使他的分数最大化。


#### 用例
输入: \[1, 5, 2]
输出: False

输入: \[1, 5, 233, 7]
输出: True

#### 解题思路
1. 直接利用dfs模拟取牌的过程，用turn表明当前玩家是先手还是后手，如果是先手，turn为1，如果是后手，turn为-1，
每次抽取时都分两种情况，取左边的或者取右边的，两种结果表示为
    - nums\[i]+dfs(i+1,j)
    - nums\[j]+dfs(i,j-1)
    
对于先手，应返回两者的最大值，对于后手，应返回两者的最小值

2. 采用动态规划算法，dp\[i]\[j]表示当数组剩下i到j的拍时，当前用户可以从这些牌里取到的最大值，所以dp\[i]\[j]
的转移方程可以写为

dp\[i]\[j]=max(nums\[i]+dp\[i+1]\[j],nums\[j]+dp\[i]\[j-1])
dp\[i]\[i]=nums\[i]

#### 代码

```python
from typing import List


class Solution:
    #利用先后手的差值表示两者分数的大小
    def PredictTheWinner(self, nums: List[int]) -> bool:
        n=len(nums)
        return self.dfs(nums,0,n-1,1)>=0


    def dfs(self,nums,left,right,turn):
        if left==right:return turn*nums[left]
        a=turn*nums[left]+self.dfs(nums,left+1,right,-turn)
        b=turn*nums[right]+self.dfs(nums,left,right-1,-turn)
        return max(a,b)


class Solution:
    #利用先后手的差值表示两者分数的大小
    def PredictTheWinner(self, nums: List[int]) -> bool:
        n=len(nums)
        dp=[[0]*n for _ in range(n)]
        for i in range(n-1,-1,-1):
            for j in range(i,n):
                if i==j:
                    dp[i][j]=nums[i]
                else:
                    dp[i][j]=max(nums[i]-dp[i+1][j],nums[j]-dp[i][j-1])
        return dp[0][n-1]>=0
```


### 题目[494. 目标和](https://leetcode-cn.com/problems/target-sum/)
给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号+和-。对于数组中的任意一个整数，你都可以从+或-中选择一个符号添加在前面。
返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

说明：
1. 数组非空，且长度不会超过20。
2. 初始的数组的和不会超过1000。
3. 保证返回的最终结果能被32位整数存下。
#### 用例
输入: nums: \[1, 1, 1, 1, 1], S: 3
输出: 5

#### 解题思路
1. 直接dfs求解，或者利用stack中间的值，方法的复杂度为O(2**n)
2. 动态规划，这道题本质上是一道01背包问题，题目已经给出了利用包中的数字可能构成的数字的范围\[-1000,1000]，所以对这样的题目
可以用dp\[i]\[j]表示用0到i个数构成数字j的方式，其转移方程可表示为：

                    dp\[i]\[j+nums\[i]+1000]+=dp\[i-1]\[j+1000]
                    dp\[i]\[j-nums\[i]+1000]+=dp\[i-1]\[j+1000]
    由于可能构成负数，所以需要+1000，由于i的状态只与i-1相关，所以可以在空间上进行优化，
3. 这道题就是找一个子集是正数，剩下是负数，他们之和等于S，假设正数集和为P，负数集合为N，有sum(P)−sum(N)=S，因为sum(nums)=sum(P)+sum(N)，
那么有sum(P)−sum(N)+sum(P)+sum(N)=S+sum(nums)，有：2∗sum(P)=S+sum(nums)，所以题目就转化成找一个正数集合之和sum(P)为(S+sum(nums))/2，当然这里的(S + sum(nums))大于0并且可以被2整除。



#### 代码

```python
from typing import List


class Solution:
    def __init__(self):
        self.ans = 0

    def findTargetSumWays(self, nums: List[int], S: int) -> int:
        n = len(nums)

        def dfs(idx, number):
            if idx == n:
                if number == S:
                    self.ans += 1
                return
            for j in [+1, -1]:
                dfs(idx + 1, number + j * nums[idx])

        dfs(0, 0)
        return self.ans


class Solution:
    def findTargetSumWays(self, nums: List[int], S: int) -> int:
        c = collections.defaultdict(int)
        c[0] = 1
        for num in nums:
            nxt = collections.defaultdict(int)
            for k, v in c.items():
                nxt[k - num] += v
                nxt[k + num] += v
            c = nxt
        return c[S]


class Solution:
    # 这道题是完全背包问题
    def findTargetSumWays(self, nums: List[int], S: int) -> int:
        n = len(nums)
        dp = [[0] * 2001 for _ in range(n)]
        dp[0][nums[0] + 1000] = 1
        dp[0][-nums[0] + 1000] += 1  # 可能nums[0]==0
        for i in range(1, n):
            for j in range(-1000, 1001):
                if dp[i - 1][j + 1000] > 0:
                    dp[i][j + nums[i] + 1000] += dp[i - 1][j + 1000]
                    dp[i][j - nums[i] + 1000] += dp[i - 1][j + 1000]
        return 0 if S > 1000 else dp[-1][S]


class Solution:
    # 这道题是完全背包问题
    def findTargetSumWays(self, nums: List[int], S: int) -> int:
        n = len(nums)
        dp = [0] * 2001
        dp[0] = 1
        dp[nums[0] + 1000] = 1
        dp[-nums[0] + 1000] += 1
        for i in range(n):
            nxt = [0] * 2001
            for j in range(-1000, 1001):
                if dp[j + 1000] > 0:
                    nxt[j + 1000 + nums[i]] += dp[j + 1000]
                    nxt[j + 1000 - nums[i]] += dp[j + 1000]
            dp = nxt
        return 0 if S > 1000 else dp[S + 1000]


class Solution:
    def findTargetSumWays(self, nums: List[int], S: int) -> int:
        t=sum(nums)
        target = S + t
        if t < S or target < 0 or target % 2: return 0
        target //= 2
        dp = [0] * (target + 1)
        dp[0] = 1
        for num in nums:
            for i in range(target, num - 1, -1):
                if i - num >=  0:
                    dp[i] += dp[i - num]
        return dp[-1]


k = Solution()
print(k.findTargetSumWays([1, 1, 1, 1, 1], 3))
```