---
title: leetcode-69
date: 2020-05-09 11:02:12
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

![图片](http://api.mtyqx.cn/api/random.php?11)
<!-- more -->

### 题目：leetcode-69
实现int sqrt(int x)函数。
计算并返回x的平方根，其中 x 是非负整数。
由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

#### 用例
---
输入：4
<br>输出：2</br>
---
输入：8
<br>输出：2</br>
说明：8的平方根是2.82842...由于返回的是整形，所以小数部分被舍去
### 解题思路

本题目有很多种解法，包括暴力法，二分法以及牛顿迭代法，下面针对牛顿法的计算原理进行说明，
牛顿法求解开平方时本质上是求解方程的近似根，对于上面的问题，我们可以将其理解为求解方程
f(x)=x^2-a的根，因此，假设他的根为gn，那么该根也是函数在gn处的切线与x轴交点，该处的切线
方程为y=kx+b,可知k=2gn,将参数带入到二次方程中可以得到b=f(gn)-2gn^2,所以切线方程
y=2gnx+f(gn)-2gn^2,当y=0时，求出来的x值就是gn+1，所以得到gn+1=gn-f(gn)/2gn,带入f(gn)=gn^2-a
,可以得到gn+1=(gn+a/gn)/2


### 代码
```python
#牛顿迭代
class Solution:
    def mySqrt(self, x: int) -> int:
        num = x
        while num * num > x:
            num = (num + x // num) // 2
        return num
```


```python
#二分法
class Solution:
    def mySqrt(self, x: int) -> int:
        if x == 0: return 0
        left, right = 0, x
        while left < right:
            mid = left + (right - left + 1) // 2
            if mid * mid > x:
                right = mid - 1
            else:
                left = mid
        return left
```