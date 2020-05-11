---
title: leetcode-50
date: 2020-05-11 10:16:40
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

![图片](http://api.mtyqx.cn/api/random.php?33)
<!-- more -->

### 题目[leetcode-50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

实现 pow(x, n) ，即计算 x 的 n 次幂函数。
说明:
-100.0 < x < 100.0
n 是 32 位有符号整数，其数值范围是 \[−231, 231 − 1\] 。


#### 用例
输入: 2.00000, 10
输出: 1024.00000

输入: 2.10000, 3
输出: 9.26100

输入: 2.00000, -2
输出: 0.25000

#### 解题思路

利用快速幂算法，采用递归计算时间复杂度O(logn),空间复杂度O(logn)
采用迭代计算时间复杂度O(logn),空间复杂度O(1)

迭代计算中以数字x^77作为例子分析迭代计算过程，其计算过程如下所示，利用加号表示
在计算过程中额外增加的x，其计算过程如下：x->x^2->x^4->+x^9->+x^19->^38->+x^77
1. x^38->+x^77中额外增加的x在x^77中贡献了一次
2. x^9->+x^19，中的x在后面一共被平方了两次，贡献了x^4
3. x^4->+x^9中的x在后面被平方了3次，贡献了x^8
4. 最初的x在之后被平方了6次，贡献了x^64

可以看到x\*x^4\*x^8\*x^64就是x^77，而他们的指数1，4，8，64恰好对应77的二进制（1001101）中的1


#### 代码


```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        flag = True
        if n < 0: flag = False
        n = abs(n)
        return self.cal(x,n) if flag else 1/self.cal(x,n)

    def cal(self, x, n):
        ans = 1
        x_contribute = x
        while n > 0:
            if n & 1:
                ans *= x_contribute
            x_contribute *= x_contribute
            n >>= 1
        return ans
```

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if x==0 and n<0:return float('inf')
        flag=True
        if n<0:flag=False
        n=abs(n)
        ans=self.cal(x,n)
        return ans if flag else 1/ans


    def cal(self,x,n):
        if n==0:return 1
        tmp=self.cal(x,n//2)
        return tmp*tmp*x if n & 1 else tmp*tmp
```