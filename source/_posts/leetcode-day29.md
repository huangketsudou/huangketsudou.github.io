---
title: leetcode-day29
date: 2020-07-22 15:33:48
tags:
- leetcode
- 算法
- java
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?461)
<!-- more -->


### 题目[剑指 Offer 11.旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。 


#### 测试用例
输入：[3,4,5,1,2]
输出：1

输入：[2,2,2,0,1]
输出：0

#### 解题思路
二分法求解
1. 情况一:num[mid]>num[right],最小值在mid+1，right的区间
2. 情况二:num[mid]<num[right],最小值在left，mid的区间
3. 情况三:num[mid]==num[right],最小值所在的区间无法判断，退化为O(n)解法，从right--开始收缩边界

#### 代码
```java
class Solution {
    public int minArray(int[] numbers) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (numbers[mid] < numbers[right]) {
                right = mid;
            } else if (numbers[mid] > numbers[right]) {
                left = mid + 1;
            } else {
                right--;
            }
        }
        return numbers[left];
    }
}
```


### 题目[面试题 08.06.汉诺塔问题]()
在经典汉诺塔问题中，有 3 根柱子及 N 个不同大小的穿孔圆盘，盘子可以滑入任意一根柱子。一开始，所有盘子自上而下按升序依次套在第一根柱子上(即每一个盘子只能放在更大的盘子上面)。移动圆盘时受到以下限制:
(1) 每次只能移动一个盘子;
(2) 盘子只能从柱子顶端滑出移到下一根柱子;
(3) 盘子只能叠在比它大的盘子上。

请编写程序，用栈将所有盘子从第一根柱子移到最后一根柱子。

你需要原地修改栈。


#### 测试用例
输入：A = [2, 1, 0], B = [], C = []
 输出：C = [2, 1, 0]
#### 解题思路
汉诺塔的移动规律为大的必须在小的的下面，可以这样思考他的移动过程
1. 当塔只有一层时，将A的移动到C，完成
2. 当塔有两层时，将A的最上端的移动到B，将下端移动到C，最后再从B移动到C，完成
3. 当塔不知两层时，将除最下层的那个看作最底，其他的作为最上端，因此又变成情况2，此时，为了移动B中的所有塔到C，将B看作A，将A看作B，又变成了情况二

#### 代码

```java
class Solution {
    public void hanota(List<Integer> A, List<Integer> B, List<Integer> C) {
        hanota1(A.size(), A, B, C);
    }

    private void hanota1(int n, List<Integer> A, List<Integer> B, List<Integer> C) {
        if (n == 1) {
            C.add(A.remove(A.size()-1));
            return;
        }
        hanota1(n - 1, A, C, B);
        C.add(A.remove(A.size() - 1));
        hanota1(n - 1, B, A, C);
    }

}
```