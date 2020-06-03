---
title: leetcode-day16
date: 2020-06-02 19:38:59
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

![图片](http://api.mtyqx.cn/api/random.php?135)
<!-- more -->

### 题目[面试题64. 求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)
求 1+2+...+n ，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。
1<=n<=10000
#### 解题思路
1. 递归算法
2. 位运算的方式，由于题目限制n小于10000,所以写14层循环就可以了
#### 代码

```java
class Solution {
    public int sumNums(int n) {
        if (n==1){
            return 1;
        }
        return n+sumNums(n-1);
    }
}

class Solution2 {
    public int sumNums(int n) {
        int a = n;
        int b = n+1;
        int ans = 0;
//        boolean flag;
//        flag = ((b & 1) > 0) && (ans += a) > 0;//利用了
        int k = 0;
        while (k<14) {
            ans += (a & 1) * b;
            a <<= 1;
            b >>= 1;
            k++;
        }//这里投机了，本来是要展开14层循环的

        return ans >>1;
    }
}
```

### 题目[525.连续数组](https://leetcode-cn.com/problems/contiguous-array/)
给定一个二进制数组, 找到含有相同数量的 0 和 1 的最长连续子数组（的长度）。

#### 用例
```
输入: [0,1]
输出: 2
```

```
输入: [0,1,0]
输出: 2
```
#### 解题思路
遇到0加一，遇到1减一，为了保证0和1相等，那么总和应该为0，当当前的和为k时，只要找到上一次和为k的值就可以得到在上一次和为k开始到现在0
和1相等
#### 代码
```java
class Solution {
    public int findMaxLength(int[] nums) {
        HashMap<Integer,Integer> map = new HashMap<>();
        int sum = 0;
        int MaxLength = 0;
        map.put(0,-1);
        for (int i = 0; i < nums.length; i++) {
            sum = sum +(nums[i]==0?1:-1);
            if (map.containsKey(sum)){
                MaxLength=Math.max(MaxLength,i-map.get(sum));
            }else {
                map.put(sum,i);
            }
        }
        return MaxLength;
    }
}
```

### 题目[526.优美的排列](https://leetcode-cn.com/problems/beautiful-arrangement/)
假设有从 1 到 N 的 N 个整数，如果从这 N 个数字中成功构造出一个数组，使得数组的第 i 位 (1 <= i <= N) 满足如下两个条件中的一个，我们就称这个数组为一个优美的排列。条件：

1. 第 i 位的数字能被 i 整除
2. i 能被第 i 位上的数字整除

现在给定一个整数 N，请问可以构造多少个优美的排列？

#### 用例
输入：7
输出：41
#### 解题思路
dfs遍历全部的可排列方式
1. 从头开始构造全部的排列方式，用一个visited记录所用过的数字
2. 构造所有的可行数组
3. 采用状态压缩和dp数组进行分析
    - 用一个n位的二进制数表示二进制中为1的数字已任意顺序放在数组的前m位（m为该二进制数中1的个数）。
    - 例子：二进制数010101，第1,3,5位是1，一共3个1，所以表示1,3,5以任意顺序放在数组前3位。
    - 状态：dp[n]表示二进制数n代表的所有排列中有效情况的数量。
    - 更新状态：对于二进制中为0的位，判断是否可以作为下一个放入数组中的数，若是则更新`dp[n | (1 << j)] += dp[n]`
    - 例子：010101中，第2位为0，判断其是否可放在数组第4位（因为数组已有3个数），显然可以（4%2==0），更新`dp[010101 | (1 << (2-1))] += dp[010101]`;
      即`dp[010111] += dp[010101]`;
      


#### 代码

```java
class Solution {
    
    public int countArrangement(int N) {
        boolean [] visited = new boolean[N+1];
        return permute(visited,1,N);
    }
    
    public int permute(boolean [] visited,int pos,int N){
        if (pos > N){
            return 1;
        }
        int count = 0;
        for (int i = 1; i <= N; i++) {
            if (!visited[i] && (pos % i ==0 || i % pos ==0)){
                visited[i] = true;
                count+=permute(visited,pos+1,N);
                visited[i] = false;
            }
        }
        return count;
    }
}
class Solution2 {
    public int countArrangement(int N) {
        int[] arr = new  int[N];
        for (int i = 0; i < N; i++) {
            arr[i] = i+1;
        }
        return permute(arr,0);
    }
    public int permute(int[] arr,int l){
        if (l == arr.length){
            return 1;
        }
        int count = 0;
        for (int i = l; i < arr.length; i++) {
            swap(arr,i,l);
            if ((l+1) % arr[l]==0 ||arr[l]% (l+1)==0){
                count+=permute(arr,l+1);
            }
            swap(arr,i,l);
        }
        return count;
    }
    public void swap(int[] arr,int i,int j){
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] =tmp;
    }
}

class Solution3 {
    public int countArrangement(int N) {
        int[] dp = new int[1<<N];
        for (int i = 0; i < N; i++) {
            dp[1<<i] = 1;

        }
        for (int i = 0; i < (1<<N); i++) {
            int tmp = i;
            int index = 1;
            while (tmp!=0){
                tmp &= (tmp-1);
                index+=1;
            }
            for (int j = 0; j < N; j++) {
                if ((i & (1<<j)) == 0 && ((j+1) % index == 0 || index % (j+1)==0)){
                    dp[1<<j | i] += dp[i];
                    System.out.println(dp[1<<j | i]);
                }
            }
        }
        return dp[(1<<N)-1];
    }
}
```