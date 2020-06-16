---
title: leetcode-day22
date: 2020-06-16 20:37:53
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

![图片](http://api.mtyqx.cn/api/random.php?789)
<!-- more -->


### 题目[297.二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)
序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。


#### 解题思路
采用先序遍历的方式进行，注意需要保留路径中的null节点，保存了之后，仅靠先序遍历就可以完成复原，而不保存就绪要中序遍历作为辅助

#### 代码
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {
    private int index = 0;
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null){
            return "None,";
        }
        String result = root.val+",";
        result+= serialize(root.left);
        result+=serialize(root.right);
        return result;
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] splitData = data.split(",");
        return decode(splitData);
    }
    public TreeNode decode(String[] splitData){
        if (splitData[index].equals("None")){
            index++;
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(splitData[index]));
        index++;
        root.left = decode(splitData);
        root.right = decode(splitData);
        return root;
    }
}
```

### 题目[1300.转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)
给你一个整数数组 arr 和一个目标值 target ，请你返回一个整数 value ，使得将数组中所有大于 value 的值变成 value 后，数组的和最接近  target （最接近表示两者之差的绝对值最小）。

如果有多种使得和最接近 target 的方案，请你返回这些整数中的最小值。

请注意，答案不一定是 arr 中的数字。

#### 用例
输入：arr = [4,9,3], target = 10
输出：3

输入：arr = [2,3,5], target = 10
输出：5

输入：arr = [60864,25176,27249,21296,20204], target = 56803
输出：11361
#### 解题思路
思路一：对数组进行排序，结果应该在数组的最小值以及最大值之间，可采用二分法进行遍历寻找答案
思路二：计算前缀和，当目标减前缀和的值除以剩余的数字个数的值，小于当前数字，即可求出结果

不论选择哪种思路，最后都需要对所选值进行判断，因为结果可能高于target，也可能小于target


#### 代码
```java
class Solution {
    public int findBestValue(int[] arr, int target) {
        Arrays.sort(arr);
        int length = arr.length;
        int[] Prefix = new int[length + 1];
        for (int i = 0; i < length; i++) {
            Prefix[i + 1] = Prefix[i] + arr[i];
        }
        System.out.println(Arrays.toString(Prefix));
        int left = 0, right = arr[length - 1], ans = -1;
        while (left <= right) {
            int mid = (left + right) / 2;
            int index = Arrays.binarySearch(arr, mid);
            //binarysearch的返回值如下：
            //[1] 搜索值不是数组元素，且在数组范围内，从1开始计数，得“ - 插入点索引值”；
            //[2] 搜索值是数组元素，从0开始计数，得搜索值的索引值；
            //[3] 搜索值不是数组元素，且大于数组内元素，索引值为 – (length + 1);
            //[4] 搜索值不是数组元素，且小于数组内元素，索引值为 – 1。
            if (index < 0) {
                index = -index - 1;
            }
            int cur = Prefix[index] + (length - index) * mid;
            if (cur <= target) {
                ans = mid;
                left = mid + 1;
            } else{ right = mid - 1;}

        }
        int choosesmall = check(arr, ans);
        int choosebig = check(arr, ans + 1);
        return Math.abs(choosesmall - target) <= Math.abs(choosebig - target) ? ans : ans + 1;

    }

    public int check(int[] arr, int x) {
        int summary = 0;
        for (int value : arr) {
            summary += Math.min(value, x);
        }
        return summary;
    }
}
```
```java
class Solution {
    public int findBestValue(int[] arr, int target) {
        Arrays.sort(arr);
        int summary = 0;
        int length = arr.length;
        for (int i = 0; i < length; i++) {
            int x = (target - summary) / (length - i);
            if (x < arr[i]){
                double t = (target*1.0-summary)/(length-i);
                if (t - x > 0.5){
                    return x+1;
                }else return x;
            }
            summary+=arr[i];
        }
        return arr[length-1];
    }
}
```

### 题目[14.最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)
编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。
#### 用例
输入: ["flower","flow","flight"]
输出: "fl"

输入: ["dog","racecar","car"]
输出: ""
#### 解题思路
思路一: 纵向对比，找到最长的前缀

思路二: 对字符串数组排序，比较最长字符串与最短字符串的公共前缀长度就可以了


#### 代码
```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        int length = Integer.MAX_VALUE;
        for (String s : strs) {
            length=Math.min(length,s.length());
        }
        if (strs.length == 0)return "";
        StringBuilder ans = new StringBuilder();
        label:
        for (int i = 0; i < length; i++) {
            char c = strs[0].charAt(i);
            for (int j = 1; j < strs.length; j++) {
                if (strs[j].charAt(i)!=c){
                    break label;
                }
            }
            ans.append(c);
        }
        return ans.toString();
    }
}
```

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length==0)return "";
        Arrays.sort(strs, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.compare(o1.length(), o2.length());
            }
        });
        String shortest = strs[0];
        String longest = strs[strs.length-1];
        StringBuilder ans = new StringBuilder();
        for (int i = 0; i < shortest.length(); i++) {
            if (shortest.charAt(i)==longest.charAt(i)){
                ans.append(shortest.charAt(i));
            }else break;
        }
        return ans.toString();
    }
}
```
