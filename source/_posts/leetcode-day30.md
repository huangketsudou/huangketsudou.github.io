---
title: leetcode-day30
date: 2020-08-13 18:13:42
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

![图片](http://api.mtyqx.cn/api/random.php?318)
<!-- more -->

### 题目[](超大数的加减法)
实现一个函数可以实现超级大数的加法操作，完成正负数之间的相加

#### 用例
     * 1. 用例
     * 输入：12345  123
     * 输出：12468
     *
     * 2. 用例
     * 输入：12345 -123
     * 输出：12222
     *
     * 3. 用例
     * 输入：12345 -12345
     * 输出：0
     *
     * 4.用例
     * 输入：-12345 123
     * 输出：-12222

#### 解题思路
用数组模拟加减法，字符串作为返回

#### 代码

```java
public class BigDigitalUtil {

    public static String bigDigitAdd(String a, String b) {
        int aLength = a.length();
        int bLength = b.length();
        char[] aArray = new StringBuilder(a).reverse().toString().toCharArray();
        char[] bArray = new StringBuilder(b).reverse().toString().toCharArray();

        int MAXLength = Math.max(aLength, bLength);
        int[] newArray = new int[MAXLength + 1];
        for (int i = 0; i < MAXLength + 1; i++) {
            int ANum = i < aLength ? (aArray[i] - '0') : 0;
            int BNum = i < bLength ? (bArray[i] - '0') : 0;
            newArray[i] = ANum + BNum;
        }
        for (int i = 0; i < MAXLength + 1; i++) {
            if (newArray[i] > 10) {
                newArray[i + 1] = newArray[i] / 10;
                newArray[i] %= 10;
            }
        }
        StringBuilder result = new StringBuilder();
        boolean hasZero = true;
        for (int i = MAXLength; i >= 0; i--) {
            if (newArray[i] == 0 && hasZero) {
                continue;
            } else {
                hasZero = false;
            }
            result.append(newArray[i]);
        }
        return result.toString();
    }

    /**
     * 两大数相减，要求a>b
     *
     * @param a
     * @param b
     * @return
     */
    public static String bigDigitSub(String a, String b) {
        int aLength = a.length();
        int bLength = b.length();
        char[] aArray = new StringBuilder(a).reverse().toString().toCharArray();
        char[] bArray = new StringBuilder(b).reverse().toString().toCharArray();
        int MAXLength = Math.max(aLength, bLength);
        int[] newArray = new int[MAXLength];
        for (int i = 0; i < MAXLength; i++) {
            int ANum = i < aLength ? aArray[i] - '0' : 0;
            int BNum = i < bLength ? bArray[i] - '0' : 0;
            newArray[i] = ANum - BNum;
        }
        for (int i = 0; i < MAXLength; i++) {
            if (newArray[i] < 0) {
                newArray[i + 1] -= 1;
                newArray[i] += 10;
            }
        }
        StringBuilder result = new StringBuilder();
        boolean hasZero = true;
        for (int i = MAXLength - 1; i >= 0; i--) {
            if (newArray[i] == 0 && hasZero) {
                continue;
            } else {
                hasZero = false;
            }
            result.append(newArray[i]);
        }
        return result.toString();
    }


    public static int compareTo(String a, String b) {
        int ALength = a.length();
        int BLength = b.length();
        if (ALength > BLength) {
            return 1;
        } else if (ALength < BLength) {
            return -1;
        } else {
            int index = 0;
            while (true) {
                if (a.charAt(index) > b.charAt(index)) {
                    return 1;
                } else if (a.charAt(index) < b.charAt(index)) {
                    return -1;
                } else {
                    if (index == ALength - 1) {
                        return 0;
                    }
                    index++;
                }
            }
        }
    }

    public static String bigDigitSum(String a,String b){
        String result = null;
        if (a.charAt(0)=='-' && b.charAt(0)=='-'){
            a = a.substring(1);
            b = b.substring(1);
            result = bigDigitAdd(a,b);
            result = "-"+result;
        }else if (a.charAt(0)=='-'||b.charAt(0)=='-'){
            if (a.charAt(0)=='-'){
                a = a.substring(1);
                int cmp = compareTo(a,b);
                if (cmp==1){
                    result = "-"+bigDigitSub(a,b);
                }else if (cmp == -1){
                    result = bigDigitSub(b,a);
                }else {
                    result = "0";
                }
            }else {
                b = b.substring(1);
                int cmp = compareTo(a,b);
                if (cmp == 1){
                    result = bigDigitSub(a,b);
                }else if (cmp == -1){
                    result = "-" + bigDigitSub(b ,a);
                }else {
                    result = "0";
                }
            }
        }else {
            result = bigDigitAdd(a,b);
        }
        return result;
    }


    public static void main(String[] args) {
        System.out.println(bigDigitSum("-12345","-123"));
        System.out.println(bigDigitSum("-12345","123"));
        System.out.println(bigDigitSum("123","-12345"));
        System.out.println(bigDigitSum("-123456","123456"));

    }


}
```


### 题目[493. 翻转对](https://leetcode-cn.com/problems/reverse-pairs/)
给定一个数组 nums ，如果 i < j 且 nums[i] > 2*nums[j] 我们就将 (i, j) 称作一个重要翻转对。

你需要返回给定数组中的重要翻转对的数量。


#### 用例
输入: [1,3,2,3,1]
输出: 2

输入: [2,4,3,5,1]
输出: 3

#### 解题思路
归并排序，排序之后检查前面的部分中nums[i]>2*nums[j]的数量，由于前面部分已经排好序了，所以随着i的增大，其符合要求
的数量也随j而增大

本质就是归并排序的应用，相似的题目还有第315题目，剑指offer的51题

#### 代码

```java
class Solution {

    public void merge(int[] nums, int start, int mid, int end) {
        int n1 = mid - start + 1, n2 = end - mid;
        int[] A = new int[n1], B = new int[n2];
        if (n1 >= 0) System.arraycopy(nums, start, A, 0, n1);
        if (n2 >= 0) System.arraycopy(nums, mid + 1, B, 0, n2);
        int i = 0, j = 0;
        for (int k = start; k <= end; k++) {
            if (j >= n2 || (i < n1 && A[i] < B[j])) {
                nums[k] = A[i++];
            } else {
                nums[k] = B[j++];
            }
        }
    }

    public int mergeSort(int[] nums, int start, int end) {
        if (start < end) {
            int mid = start + (end - start) / 2;
            int count = mergeSort(nums, start, mid) + mergeSort(nums, mid + 1, end);
            int j = mid + 1;
            for (int i = start; i <= mid; i++) {
                while (j <= end && nums[i] > nums[j] * 2L) {//这里需要考虑整数的溢出
                    j++;
                }
                count += j - (mid + 1);
            }
            merge(nums, start, mid, end);
            System.out.println(Arrays.toString(nums));
            return count;
        }
        return 0;
    }

    public int reversePairs(int[] nums) {
        return mergeSort(nums, 0, nums.length - 1);
    }

    public static void main(String[] args) {
        int[] B = new int[]{2, 4, 3, 5, 1};
        System.out.println(new Solution11().reversePairs(B));
    }
}
```


### 题目[43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)
给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

#### 用例
输入: num1 = "2", num2 = "3"
输出: "6"


输入: num1 = "123", num2 = "456"
输出: "56088"
#### 解题思路
用一个数组来模拟乘法过程


#### 代码
```java
class Solution {
    public String multiply(String num1, String num2) {
        int n1 = num1.length(), n2 = num2.length();
        ArrayList<Integer> result = new ArrayList<>();

        for (int i = n1 - 1; i >= 0; i--) {
            int fwd = n1 - i - 1;
            for (int j = n2 - 1; j >= 0; j--) {
                int number = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                if (fwd >= result.size()) {
                    result.add(number);
                } else {
                    result.set(fwd, result.get(fwd) + number);
                }
                fwd++;
            }

        }
        int carry = 0;
        int pos;
        for (int i = 0; i < result.size(); i++) {
            pos = result.get(i);
            result.set(i, (pos + carry) % 10);
            carry = (pos + carry) / 10;
        }
        while (carry != 0) {
            result.add(carry % 10);
            carry /= 10;
        }
        StringBuilder strResult = new StringBuilder();
        result.forEach(strResult::append);
        String answer = strResult.reverse().toString();
        int k = 0;
        while (k < answer.length()){
            if (answer.charAt(k)=='0'){
                k++;
            }else {
                break;
            }
        }
        if (k==answer.length()){
            answer="0";
        }else {
            answer = answer.substring(k);
        }
        return answer;
    }
}
```

### 题目[546. 移除盒子](https://leetcode-cn.com/problems/remove-boxes/)
给出一些不同颜色的盒子，盒子的颜色由数字表示，即不同的数字表示不同的颜色。
你将经过若干轮操作去去掉盒子，直到所有的盒子都去掉为止。每一轮你可以移除具有相同颜色的连续 k 个盒子（k >= 1），这样一轮之后你将得到 k*k 个积分。
当你将所有盒子都去掉之后，求你能获得的最大积分和。

#### 用例
输入：boxes = [1,3,2,2,2,3,4,3,1]
输出：23

#### 解题思路
参考两个官方题解

https://leetcode-cn.com/problems/remove-boxes/solution/yi-chu-he-zi-by-leetcode-solution/

https://leetcode-cn.com/problems/remove-boxes/solution/guan-fang-fang-fa-2ji-yi-hua-sou-suo-dong-hua-tu-j/

#### 代码
```java
class Solution {
    public int removeBoxes(int[] boxes) {
        int[][][] dp = new int[100][100][100];
        return calculatePoints(boxes, dp, 0, boxes.length - 1, 0);
    }

    public int calculatePoints(int[] boxes, int[][][] dp, int l, int r, int k) {
        if (l > r) return 0;
        if (dp[l][r][k] != 0) return dp[l][r][k];
        while (r > l && boxes[r] == boxes[r - 1]) {
            r--;
            k++;
        }
        dp[l][r][k] = calculatePoints(boxes, dp, l, r - 1, 0) + (k + 1) * (k + 1);
        for (int i = l; i < r; i++) {
            if (boxes[i] == boxes[r]) {
                dp[l][r][k] = Math.max(dp[l][r][k], calculatePoints(boxes, dp, l, i, k + 1) + calculatePoints(boxes, dp, i + 1, r - 1, 0));
            }
        }
        return dp[l][r][k];
    }
}
```
