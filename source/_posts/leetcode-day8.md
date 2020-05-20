---
title: leetcode-day8
date: 2020-05-20 11:44:18
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

### 题目1371[每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)
给你一个字符串s，请你返回满足以下条件的最长子字符串的长度：每个元音字母，即 'a'，'e'，'i'，'o'，'u' ，在子字符串中都恰好出现了偶数次。


#### 用例
输入：s = "eleetminicoworoep"
输出：13

输入：s = "leetcodeisgreat"
输出：5

#### 解题思路
转载自leetcode官方[题解](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/solution/mei-ge-yuan-yin-bao-han-ou-shu-ci-de-zui-chang-z-2/)

我们先来考虑暴力方法怎么做。最直观的方法无非就是枚举所有子串，遍历子串中的所有字符，统计元音字母出现的个数。如果符合条件，我们就
更新答案，但这样肯定会因为超时而无法通过所有测试用例。

再回顾一下上面的操作，其实每个子串对应着一个区间，那么有什么方法可以在不重复遍历子串的前提下，快速求出这个区间里元音字母出现的次
数呢？答案就是前缀和，对于一个区间，我们可以用两个前缀和的差值，得到其中某个字母的出现次数。

我们对每个元音字母维护一个前缀和，定义pre\[i]\[k]表示在字符串前i个字符中，第 k 个元音字母一共出现的次数。假设我们需要求出\[l,r]
这个区间的子串是否满足条件，那么我们可以用pre\[r]\[k]−pre\[l−1]\[k]，在O(1) 的时间得到第 k个元音字母出现的次数。对于每一个元音
字母，我们都判断一下其是否出现偶数次即可。

我们利用前缀和优化了统计子串的时间复杂度，然而枚举所有子串的复杂度仍需要 O(n^2)，不足以通过本题，还需要继续进行优化，避免枚举所有
子串。我们考虑枚举字符串的每个位置 i，计算以它结尾的满足条件的最长字符串长度。其实我们要做的就是快速找到最小的 j∈\[0,i)，满足 
pre\[i]\[k]−pre\[j]\[k]（即每一个元音字母出现的次数）均为偶数，那么以 i结尾的最长字符串 s\[j+1,i] 长度就是i−j。

有经验的读者可能马上就想到了利用哈希表来优化查找的复杂度，但是单单利用前缀和，我们无法找到 ii 和 jj 相关的恒等式，像 1248. 统计
优美子数组 这道题我们是能明确知道两个前缀的差值是恒定的。那难道就没办法了么？其实不然，这道题我们还有一个性质没有充分利用：我们
需要找的子串中，每个元音字母都恰好出现了偶数次。

偶数这个条件其实告诉了我们，对于满足条件的子串而言，两个前缀和pre\[i]\[k] 和 pre\[j]\[k]的奇偶性一定是相同的，因为小学数学的知
识告诉我们：奇数减奇数等于偶数，偶数减偶数等于偶数。因此我们可以对前缀和稍作修改，从维护元音字母出现的次数改作维护元音字母出现次
数的奇偶性。这样我们只要实时维护每个元音字母出现的奇偶性，那么s\[j+1,i] 满足条件当且仅当对于所有的k，pre\[i]\[k] 和
pre\[j]\[k] 的奇偶性都相等，此时我们就可以利用哈希表存储每一种奇偶性（即考虑所有的元音字母）对应最早出现的位置，边遍历边更新答案。

考虑到出现次数的奇偶性其实无非就两个值，0代表出现了偶数次，1代表出现了奇数次，我们可以将其压缩到一个二进制数中，第k位的0或1代表
了第k个元音字母出现的奇偶性。举一个例子，假如到第 i个位置，u o i e a 出现的奇偶性分别为 1 1 0 0 1，那么我们就可以将其压成一个二
进制数11001作为它的状态。这样我们就可以将 55 个元音字母出现次数的奇偶性压缩到了一个二进制数中，且连续对应了二进制数的\[(00000),(11111)]
的范围，转成十进制数即\[0,31]。因此我们也不再需要使用哈希表，直接用一个长度为 3232 的数组来存储对应状态出现的最早位置即可。


简而言之，这道题的解题难点在于
1. 需要在O(n)时间内完成遍历
2. 当以某一个字母为结尾而有一个元音字母出现次数为奇数次时，需要在O(1)的时间内找到使该元音字母次数变为偶数，且其他元音字母也为偶数的*最早*出现的索引
3. 对每种元音字母出现的奇偶状态进行状态压缩

#### 代码

```python
class Solution:
    def findTheLongestSubstring(self, s: str) -> int:
        ans, status, n = 0, 0, len(s)
        pos = [-1] * (1 << 5)
        pos[0] = 0

        for i in range(n):
            if s[i] == 'a':
                status ^= 1 << 0
            elif s[i] == 'e':
                status ^= 1 << 1
            elif s[i] == 'i':
                status ^= 1 << 2
            elif s[i] == 'o':
                status ^= 1 << 3
            elif s[i] == 'u':
                status ^= 1 << 4
            if pos[status] != -1:
                ans = max(ans, i + 1 - pos[status])
            else:
                pos[status] = i + 1
        return ans
```


### 题目478[在圆内随机生成点](https://leetcode-cn.com/problems/generate-random-point-in-a-circle/)
给定圆的半径和圆心的 x、y 坐标，写一个在圆中产生均匀随机点的函数 randPoint 。

说明:

输入值和输出值都将是浮点数。
圆的半径和圆心的 x、y 坐标将作为参数传递给类的构造函数。
圆周上的点也认为是在圆中。
randPoint 返回一个包含随机点的x坐标和y坐标的大小为2的数组。

#### 解题思路
拒绝采样

#### 代码
```python
import random
class Solution:

    def __init__(self, radius: float, x_center: float, y_center: float):
        self.radius = radius
        self.x = x_center
        self.y = y_center

    def randPoint(self) -> List[float]:
        while True:
            x = self.x - self.radius + random.random() * 2 * self.radius
            y = self.y - self.radius + random.random() * 2 * self.radius
            if (x - self.x) ** 2 + (y - self.y) ** 2 <=self.radius**2:
                return [x,y]
```

### 题目481[神奇字符串](https://leetcode-cn.com/problems/magical-string/)
神奇的字符串 S 只包含 '1' 和 '2'，并遵守以下规则：

字符串 S 是神奇的，因为串联字符 '1' 和 '2' 的连续出现次数会生成字符串 S 本身。
字符串 S 的前几个元素如下：S = “1221121221221121122 ......”

如果我们将 S 中连续的 1 和 2 进行分组，它将变成：
1 22 11 2 1 22 1 22 11 2 11 22 ......

并且每个组中 '1' 或 '2' 的出现次数分别是：
1 2 2 1 1 2 1 2 2 1 2 2 ......

你可以看到上面的出现次数就是 S 本身。
给定一个整数 N 作为输入，返回神奇字符串 S 中前 N 个数字中的 '1' 的数目。



#### 解题思路
采用模拟法模拟数据字符的生成，指针指向头部某一字符，代表本次插入要插入一个或者两个字符，插入完成后，指针指向下一字符，利用一个状态转移方程来表示本次应该插入1还是2，方程为

x=2-x//2

x的值只会取1或者2，并用ones记录插入的1的数量
注意：最后以string的长度作为终止条件时，结束循环的最后一次插入可能插入1个字符，也可能插入两个，当插入两个1时，ones的计数多了1个，所以需要减去1，例如n=13,结束循环时，string=12211212212211，len(string)=14,其中的1共有7个，减去1才是需要的结果。

#### 代码
```python
class Solution:
    def magicalString(self, n: int) -> int:
        if n==0:return 0
        string = '122'
        i = 2
        char = 2
        ones=1
        while len(string) < n:
            char = 2 - char // 2
            if char==1:
                ones+=int(string[i])
            string += int(string[i])*str(char)
            i+=1
        return ones-1 if char==1 and len(string)>n else ones
```