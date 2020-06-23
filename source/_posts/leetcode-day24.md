---
title: leetcode-day24
date: 2020-06-23 23:55:12
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

![图片](http://api.mtyqx.cn/api/random.php?143)
<!-- more -->


### 题目[67.二进制求和](https://leetcode-cn.com/problems/add-binary/)
给你两个二进制字符串，返回它们的和（用二进制表示）。

输入为 非空 字符串且只包含数字 1 和 0。

#### 用例
输入: a = "11", b = "1"
输出: "100"

输入: a = "1010", b = "1011"
输出: "10101"

#### 解题思路
1. 模拟法：模拟二进制加法的过程
2. 解析字符串

#### 代码
```java
class Solution {
    public String addBinary(String a, String b) {
        int length = Math.max(a.length(), b.length());
        StringBuilder answer = new StringBuilder();
        int carry = 0;
        for (int i = 0; i < length; i++) {
            carry += i >= a.length() ? 0 : a.charAt(a.length() - 1 - i) - '0';
            carry += i >= b.length() ? 0 : b.charAt(b.length() - 1 - i) - '0';
            answer.append(carry % 2);
            carry /= 2;
        }
        if (carry==1){
            answer.append(carry);
        }
        answer.reverse();
        return answer.toString();
    }
}


class Solution2 {
    public String addBinary(String a, String b) {
        return Integer.toBinaryString(
                Integer.parseInt(a, 2) + Integer.parseInt(b, 2)
        );
    }
}
```

### 题目[面试题 16.18. 模式匹配](https://leetcode-cn.com/problems/pattern-matching-lcci/)
你有两个字符串，即pattern和value。 pattern字符串由字母"a"和"b"组成，用于描述字符串中的模式。例如，字符串"catcatgocatgo"匹配模式"aabab"（其中"cat"是"a"，"go"是"b"），该字符串也匹配像"a"、"ab"和"b"这样的模式。但需注意"a"和"b"不能同时表示相同的字符串。编写一个方法判断value字符串是否匹配pattern字符串。


#### 用例
输入： pattern = "abba", value = "dogcatcatdog"
输出： true

输入： pattern = "abba", value = "dogcatcatfish"
输出： false

#### 解题思路
基本是暴力匹配，但是可以通过很多条件进行剪枝



#### 代码

```java
class Solution {
    public boolean patternMatching(String pattern, String value) {
        int count_a = 0, count_b = 0;
        for (char ch: pattern.toCharArray()) {
            if (ch == 'a') {
                ++count_a;
            } else {
                ++count_b;
            }
        }
        if (count_a < count_b) {
            int temp = count_a;
            count_a = count_b;
            count_b = temp;
            char[] array = pattern.toCharArray();
            for (int i = 0; i < array.length; i++) {
                array[i] = array[i] == 'a' ? 'b' : 'a';
            }
            pattern = new String(array);
        }
        if (value.length() == 0) {
            return count_b == 0;
        }
        if (pattern.length() == 0) {
            return false;
        }
        for (int len_a = 0; count_a * len_a <= value.length(); ++len_a) {
            int rest = value.length() - count_a * len_a;
            if ((count_b == 0 && rest == 0) || (count_b != 0 && rest % count_b == 0)) {
                int len_b = (count_b == 0 ? 0 : rest / count_b);
                int pos = 0;
                boolean correct = true;
                String value_a = "", value_b = "";
                for (char ch: pattern.toCharArray()) {
                    if (ch == 'a') {
                        String sub = value.substring(pos, pos + len_a);
                        if (value_a.length() == 0) {
                            value_a = sub;
                        } else if (!value_a.equals(sub)) {
                            correct = false;
                            break;
                        }
                        pos += len_a;
                    } else {
                        String sub = value.substring(pos, pos + len_b);
                        if (value_b.length() == 0) {
                            value_b = sub;
                        } else if (!value_b.equals(sub)) {
                            correct = false;
                            break;
                        }
                        pos += len_b;
                    }
                }
                if (correct && !value_a.equals(value_b)) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

### 题目[124.二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。


#### 解题思路
递归——递归思路如下：
1. 以一个结点作为根节点，与其左右结点的最大贡献值之和求和
2. 返回根节点与左右节点以及0的最大值的求和作为向上的贡献。

#### 代码
```java
class Solution {
    int maxSum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }

    public int maxGain(TreeNode node) {
        if (node == null) {
            return 0;
        }
        
        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);

        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int priceNewpath = node.val + leftGain + rightGain;

        // 更新答案
        maxSum = Math.max(maxSum, priceNewpath);

        // 返回节点的最大贡献值
        return node.val + Math.max(leftGain, rightGain);
    }
}
```



### 题目[]()
给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

#### 用例
输入:
s = "aa"
p = "a"
输出: false



输入:
s = "aa"
p = "a*"
输出: true
#### 解题思路
动态规划：状态转移方程


```python
if p[i - 1] != "*":
                    dp[i][j] = dp[i - 1][j - 1] and (p[i - 1] == '.' or p[i - 1] == s[j - 1])
                else:
                    dp[i][j] = dp[i - 2][j] or (
                                (p[i - 2] == '.' or p[i - 2] == s[j - 1]) and dp[i][j - 1])
```

#### 代码

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(p), len(s)
        dp = [[False] * (n + 1) for _ in range(m + 1)]
        for i in range(m + 1):
            if i == 0:
                dp[i][0] = True
            elif p[i - 1] == '*':
                dp[i][0] = dp[i - 2][0]
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if p[i - 1] != "*":
                    dp[i][j] = dp[i - 1][j - 1] and (p[i - 1] == '.' or p[i - 1] == s[j - 1])
                else:
                    dp[i][j] = dp[i - 2][j] or (
                                (p[i - 2] == '.' or p[i - 2] == s[j - 1]) and dp[i][j - 1])
        return dp[m][n]
```