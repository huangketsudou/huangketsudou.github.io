---
title: leetcode-day6
date: 2020-05-18 10:36:56
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

![图片](http://api.mtyqx.cn/api/random.php?4)
<!-- more -->


### 题目152[乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)
给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

#### 用例
输入: [2,3,-2,4]
输出: 6

输入: [-2,0,-1]
输出: 0
#### 解题思路
思路一：采用动态规划方式，为了求得数组中连乘积的最大值，用dp\[i]表示以i为结尾的连乘积的最大值，但是
由于数组中允许有负数，而负数乘以最大正数会使其变为最小数，而负数乘以最小负数，可能会变为最大正数，
所以还需要用dpmin\[i]保留以i为结尾的连乘积的最小数，另外，考虑数组中有0的情况，0乘以任何结果最终都会为0，因此当连乘计算，如果dp\[i-1]==0,
那么dp\[i]=max(dp\[i-1],nums\[i]),dpmin\[i]=min(dpmin\[i-1],nums\[i])
所以最终的状态转移函数为premax\[i]=max(premax\[i-1]*nums\[i],premin\[i-1]*nums\[i],nums\[i])
,premin\[i]=min(premax\[i-1]*nums\[i],premin\[i-1]*nums\[i],nums\[i])

思路二：对于全为正数的数组，全部乘起来可得最大值，对于含有负数的数组（不含0），如果负数个数为偶数，全部相乘可得最大值，
如果负数个数为奇数，例如\[-1,-2,-3]，如果从左往右乘，最大值为2，如果从右往左乘，最大值为6

#### 代码
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        reverse_nums = nums[::-1]
        for i in range(1, len(nums)):
            nums[i] *= nums[i - 1] or 1 #遇到0，乘以1,连乘连加常用的方法
            reverse_nums[i] *= reverse_nums[i - 1] or 1
        return max(nums + reverse_nums)



class Solution2:
    def maxProduct(self, nums: List[int]) -> int:
        res = nums[0]
        pre_max = nums[0]
        pre_min = nums[0]
        for num in nums[1:]:
            cur_max = max(pre_max * num, pre_min * num, num)
            cur_min = min(pre_max * num, pre_min * num, num)
            res = max(res, cur_max)
            pre_max = cur_max
            pre_min = cur_min
        return res
```


### 题目468[验证IP地址](https://leetcode-cn.com/problems/validate-ip-address/)
编写一个函数来验证输入的字符串是否是有效的 IPv4 或 IPv6 地址。
IPv4 地址由十进制数和点来表示，每个地址包含4个十进制数，其范围为 0 - 255， 用(".")分割。比如，172.16.254.1；
同时，IPv4 地址内的数不会以 0 开头。比如，地址 172.16.254.01 是不合法的。

IPv6 地址由8组16进制的数字来表示，每组表示 16 比特。这些组数字通过 (":")分割。比如,2001:0db8:85a3:0000:0000:8a2e:0370:7334 是一个有效的地址。而且，我们可以加入一些以 0 开头的数字，字母可以使用大写，也可以是小写。所以， 2001:db8:85a3:0:0:8A2E:0370:7334 也是一个有效的 IPv6 address地址 (即，忽略 0 开头，忽略大小写)。
然而，我们不能因为某个组的值为 0，而使用一个空的组，以至于出现 (::) 的情况。比如，2001:0db8:85a3::8A2E:0370:7334 是无效的 IPv6 地址。
同时，在 IPv6 地址中，多余的 0 也是不被允许的。比如， 02001:0db8:85a3:0000:0000:8a2e:0370:7334 是无效的。


#### 用例
输入: "172.16.254.1"

输出: "IPv4"

输入: "2001:0db8:85a3:0:0:8A2E:0370:7334"

输出: "IPv6"

输入: "256.256.256.256"

输出: "Neither"
#### 解题思路
re正则表达式或者对数据进行切分

#### 代码
```python
import re


class Solution:
    chunk_IPv4 = r'([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])'
    patten_IPv4 = re.compile(r'^(' + chunk_IPv4 + r'\.){3}' + chunk_IPv4 + r'$')

    chunk_IPv6 = r'([0-9a-fA-F]{1,4})'
    patten_IPv6 = re.compile(r'^(' + chunk_IPv6 + r'\:){7}' + chunk_IPv6 + r'$')
    def validIPAddress(self, IP: str) -> str:
        #正则表达式匹配

        if '.' in IP:
            return "IPv4" if self.patten_IPv4.match(IP) else "Neither"
        if ':' in IP:
            return "IPv6" if self.patten_IPv6.match(IP) else "Neither"
        return "Neither"


class Solution:
    def validate_IPv4(self, IP: str) -> str:
        nums = IP.split('.')
        for x in nums:
            # Validate integer in range (0, 255):
            # 1. length of chunk is between 1 and 3
            if len(x) == 0 or len(x) > 3:
                return "Neither"
            # 2. no extra leading zeros
            # 3. only digits are allowed
            # 4. less than 255
            if x[0] == '0' and len(x) != 1 or not x.isdigit() or int(x) > 255:
                return "Neither"
        return "IPv4"

    def validate_IPv6(self, IP: str) -> str:
        nums = IP.split(':')
        hexdigits = '0123456789abcdefABCDEF'
        for x in nums:
            # Validate hexadecimal in range (0, 2**16):
            # 1. at least one and not more than 4 hexdigits in one chunk
            # 2. only hexdigits are allowed: 0-9, a-f, A-F
            if len(x) == 0 or len(x) > 4 or not all(c in hexdigits for c in x):
                return "Neither"
        return "IPv6"

    def validIPAddress(self, IP: str) -> str:
        if IP.count('.') == 3:
            return self.validate_IPv4(IP)
        elif IP.count(':') == 7:
            return self.validate_IPv6(IP)
        else:
            return "Neither"

from ipaddress import ip_address, IPv6Address
class Solution:
    def validIPAddress(self, IP: str) -> str:
        try:
            return "IPv6" if type(ip_address(IP)) is IPv6Address else "IPv4"
        except ValueError:
            return "Neither"

```


### 题目470[用 Rand7() 实现 Rand10()](https://leetcode-cn.com/problems/implement-rand10-using-rand7/)
已有方法 rand7 可生成 1 到 7 范围内的均匀随机整数，试写一个方法 rand10 生成 1 到 10 范围内的均匀随机整数。
不要使用系统的 Math.random() 方法。

#### 解题思路
我们可以用拒绝采样的方法实现 Rand10()。在拒绝采样中，如果生成的随机数满足要求，
那么久返回该随机数，否则会不断生成直到一个满足要求的随机数为止。若我们调用两 
Rand7()，那么可以生成 \[1, 49] 之间的随机整数，我们只用到其中的 40 个，用来实现
 Rand10()，而拒绝剩下的 9 个数。

#### 代码
```python
class Solution:
    def rand10(self):
        """
        :rtype: int
        """
        while True:
            row = rand7()
            col = rand7()
            idx = col + (row - 1) * 7
            if idx<=40:
                return 1+(idx-1)%10
```