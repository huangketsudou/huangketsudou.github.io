---
title: leetcode-day31
date: 2020-08-19 08:54:06
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

![图片](http://api.mtyqx.cn/api/random.php?781)
<!-- more -->

### 题目[647.回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)
给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

#### 用例
输入："abc"
输出：3

输入："aaa"
输出：6


#### 解题思路
思路一：中心扩展法
思路二：马拉车算法__待补充

#### 代码
```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        int result = 0;
        for (int i = 0; i < n; i++) {
            result += SubStrings(s, i, i, n);
            result += SubStrings(s, i, i + 1, n);
        }
        return result;
    }

    private int SubStrings(String s, int left, int right, int length) {
        int count = 0;
        while (left >= 0 && right < length && s.charAt(left) == s.charAt(right)) {
            count++;
            left--;
            right++;
        }
        return count;
    }
}
```

### 题目[剑指 Offer 31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。



#### 用例
输入：pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
输出：true


输入：pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
输出：false
#### 解题思路
解用一个辅助栈来模拟栈的操作，栈顶元素与弹出栈相同时就弹出，直到栈为空，如果栈不为空，就失败

#### 代码
```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        LinkedList<Integer> stack = new LinkedList<>();
        int i = 0, j = 0;
        while (i < pushed.length) {
            stack.push(pushed[i++]);
            while (!stack.isEmpty() && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        return stack.isEmpty();
    }
}
```

### 题目[剑指 Offer 33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 true，否则返回 false。假设输入的数组的任意两个数字都互不相同。

#### 用例
输入: [1,6,3,2,5]
输出: false

输入: [1,3,2,6,5]
输出: true
#### 解题思路
思路一：二叉搜索树要求其后续遍历前一部分的比根小，后一部分比根大，所以可以用先遍历小的部分，在检查大的部分是否满足全部比较大，最后在对子集进行检查
思路二：利用单调栈
1. 借助一个单调栈 stackstack 存储值递增的节点；
2. 每当遇到值递减的节点 r_i，则通过出栈来更新节点 r_i的父节点 rootroot；
3. 每轮判断 r_i和 rootroot 的值关系：
    - 若 r_i >root 则说明不满足二叉搜索树定义，直接返回 false。
    - 若 r_i < root 则说明满足二叉搜索树定义，则继续遍历。


#### 代码
```java
class Solution65 {
    public boolean verifyPostorder(int[] postorder) {
        return find(postorder, 0, postorder.length - 1);
    }

    private boolean find(int[] postorder, int left, int right) {
        if (left >= right) {
            return true;
        }
        int idx = left;
        while (postorder[idx] < postorder[right]) {
            idx++;
        }
        int rIdx = idx;
        while (postorder[rIdx] > postorder[right]) rIdx++;
        return rIdx == right && find(postorder, left, idx - 1) && find(postorder, idx, right - 1);
    }

}

class Solution66 {
    public boolean verifyPostorder(int[] postorder) {
        LinkedList<Integer> stack = new LinkedList<>();
        int root = Integer.MAX_VALUE;
        for (int i = postorder.length - 1; i >= 0; i--) {
            if (postorder[i] > root) return false;
            while (!stack.isEmpty() && stack.peek()>postorder[i]){
                root = stack.pop();
            }
            stack.push(postorder[i]);
        }
        return true;
    }
}
```



### 题目[]()


#### 用例


#### 解题思路


#### 代码