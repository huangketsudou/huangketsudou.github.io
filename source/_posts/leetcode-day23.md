---
title: leetcode-day23
date: 2020-06-17 19:48:10
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

![图片](http://api.mtyqx.cn/api/random.php?1)
<!-- more -->

### 题目[1014.最佳观光组合](https://leetcode-cn.com/problems/best-sightseeing-pair/)
给定正整数数组 A，A[i] 表示第 i 个观光景点的评分，并且两个景点 i 和 j 之间的距离为 j - i。

一对景点（i < j）组成的观光组合的得分为（A[i] + A[j] + i - j）：景点的评分之和减去它们两者之间的距离。

返回一对观光景点能取得的最高分。


#### 用例
输入：[8,1,5,2,6]
输出：11
解释：i = 0, j = 2, A[i] + A[j] + i - j = 8 + 5 + 0 - 2 = 11

#### 解题思路
A[i]+A[j]+i-j可以拆分为A[i]+i和A[j]-j两部分，对于固定的j，只要能保证A[i]+i为最大值，就能保证对当前的j，点数最大，而A[i]+i得最大值
在遍历过程中记录就可以了，可以在O(N)内完成工作

技巧：两个在变的东西，拆分为两部分，一部分动一部分不动

#### 代码
```java
class Solution {
    public int maxScoreSightseeingPair(int[] A) {
        int maxPrefix = A[0];
        int ans = 0;
        for (int i = 1; i < A.length; i++) {
            ans = Math.max(maxPrefix+A[i]-i,ans);
            maxPrefix = Math.max(maxPrefix,A[i]+i);
        }
        return ans;
    }
}
```

### 题目[1028.从先序遍历还原二叉树](https://leetcode-cn.com/problems/recover-a-tree-from-preorder-traversal/)

我们从二叉树的根节点 root 开始进行深度优先搜索。

在遍历中的每个节点处，我们输出 D 条短划线（其中 D 是该节点的深度），然后输出该节点的值。（如果节点的深度为 D，则其直接子节点的深度为 D + 1。根节点的深度为 0）。

如果节点只有一个子节点，那么保证该子节点为左子节点。

给出遍历输出 S，还原树并返回其根节点 root。

#### 用例
输入："1-2--3--4-5--6--7"
输出：[1,2,5,3,4,6,7]

#### 解题思路
利用栈进行遍历构造，读取字符串中‘-’的个数代表当前要构造的结点的深度是多少，当当前结点的深度大于上一次结点的深度，
连接到上一个结点的左节点，否则，弹出最后一个结点直到它的深度小于要构造的结点的深度，然后把新构造的结点连接到其右结点上。
并以此进行遍历。

#### 代码
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
    }
}

class Solution {
    public TreeNode recoverFromPreorder(String S) {
        int length = S.length();
        int idx = 0;
        int tmp = 0;
        while (idx < length) {
            if (S.charAt(idx) == '-') break;
            tmp = 10 * tmp + S.charAt(idx++) - '0';
        }
        ArrayDeque<TreeNode> stack = new ArrayDeque<>();
        stack.addLast(new TreeNode(tmp));
        int level = 0;
        while (idx < length) {
            int currentLevel = 0;
            while (S.charAt(idx) == '-') {//这个地方不可以用S.charAt(idx++)，因为这里直接++会导致不等于的时候也++
                currentLevel++;
                idx++;
            }
            tmp = 0;
            while (idx<length && Character.isDigit(S.charAt(idx))) {
                tmp = 10 * tmp  + S.charAt(idx++) - '0';
            }
            TreeNode node = new TreeNode(tmp);
            if (currentLevel > level) {
                stack.getLast().left = node;
            }
            else {
                while (currentLevel <= level) {
                    stack.pollLast();
                    level--;
                }
                stack.getLast().right = node;
            }
            stack.addLast(node);
            level++;
        }

        return stack.peekFirst();
    }
}
```

### 题目[125.验证回文串](https://leetcode-cn.com/problems/valid-palindrome/)
给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

#### 用例
输入: "A man, a plan, a canal: Panama"
输出: true


输入: "race a car"
输出: false
#### 解题思路
利用双指针进行遍历，只要遍历的过程中左右结点不同就返回false

#### 代码

```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        s = s.toLowerCase();
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                left++;
            }
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                right--;
            }
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
}
```