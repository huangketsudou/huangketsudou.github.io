---
title: leetcode-day26
date: 2020-07-04 22:41:48
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


### 题目[剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)
统计一个数字在排序数组中出现的次数。

#### 用例
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2


输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
#### 解题思路
思路一：遍历数组并统计
思路二：利用二分法查找边界，左边界为第一个小于target的数，右边界为第一个大于target的数，
有一种思路是查找左右第一个等于target的数，但是由于数组中这个数不一定会出现，这会导致多出很多不必要的判断

#### 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] <= target) {
                left = mid + 1;
            } else {
                right = mid -1;
            }
        }
        int rightBound = left;
        left = 0;right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid-1;
            }
        }
        int leftBound = right;
        return rightBound - leftBound - 1;
    }
}
```



### 题目[面试题 17.13.恢复空格](https://leetcode-cn.com/problems/re-space-lcci/)
哦，不！你不小心把一个长篇文章中的空格、标点都删掉了，并且大写也弄成了小写。像句子"I reset the computer. It still didn’t boot!"已经变成了"iresetthecomputeritstilldidntboot"。
在处理标点符号和大小写之前，你得先把它断成词语。当然了，你有一本厚厚的词典dictionary，不过，有些词没在词典里。假设文章用sentence表示，设计一个算法，把文章断开，要求未识别的字符最少，返回未识别的字符数。


#### 用例
输入：
dictionary = ["looked","just","like","her","brother"]
sentence = "jesslookedjustliketimherbrother"
输出： 7


#### 解题思路
基本的动态规划思路，把出现在字典中的字符串的长度看作0，记录以某个位置i的字符为结尾可以找到的最短的字符串。
状态转移方程：dp[i] = min(dp[i],dp[j]) if (string[j:i] in dictionary)

上面的基本动态规划思路会频繁地创建字符串，导致时间消耗，因此采用一个字典树来解决频繁这个问题，还可以实现对子串的重用


#### 代码

```java
class Solution {
    /*
    对于一段字符串查找目标，可以这样理解如果能从中找到一个在字典中的字符串，就认为该字符串的长度为0，剩下的就是找不到的值
    这里实现的是一个反向的字典树，所以要从后往前找
     */
    /*
    可以实现一个正向的字典树，但是dp就必须从后向前
     */
    public int respace(String[] dictionary, String sentence) {
        int n = sentence.length();

        Trie root = new Trie();
        for (String word : dictionary) {
            root.insert(word);
        }

        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for (int i = 1; i <= n; ++i) {
            dp[i] = dp[i - 1] + 1;

            Trie curPos = root;
            for (int j = i; j >= 1; --j) {
                int t = sentence.charAt(j - 1) - 'a';
                if (curPos.next[t] == null) {
                    break;
                } else if (curPos.next[t].isEnd) {
                    dp[i] = Math.min(dp[i], dp[j - 1]);
                }
                if (dp[i] == 0) {
                    break;
                }
                curPos = curPos.next[t];
            }
        }
        return dp[n];
    }

    public int respace2(String[] dictionary, String sentence) {
        int n = sentence.length();

        BeforeTrie bt = new BeforeTrie();
        for (String word : dictionary) {
            bt.insert(word);
        }
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[n] = 0;
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = dp[i + 1] + 1;
            BeforeTrie curPos = bt;
            for (int j = i; j < n; j++) {
                int t = sentence.charAt(j) - 'a';
                if (curPos.next[t] == null) {
                    break;
                } else if (curPos.next[t].isEnd) {
                    dp[i] = Math.min(dp[i], dp[j + 1]);
                    System.out.println(i + " " + j);
                }
                if (dp[i] == 0) {
                    break;
                }
                curPos = curPos.next[t];
            }
        }
        return dp[0];
    }
}

class Trie {
    public Trie[] next;
    public boolean isEnd;

    public Trie() {
        next = new Trie[26];
        isEnd = false;
    }

    public void insert(String s) {
        Trie curPos = this;

        for (int i = s.length() - 1; i >= 0; --i) {
            int t = s.charAt(i) - 'a';
            if (curPos.next[t] == null) {
                curPos.next[t] = new Trie();
            }
            curPos = curPos.next[t];
        }
        curPos.isEnd = true;
    }
}

class BeforeTrie {
    public BeforeTrie[] next;
    public boolean isEnd;

    public BeforeTrie() {
        next = new BeforeTrie[26];
        isEnd = false;
    }

    public void insert(String s) {
        BeforeTrie curPos = this;

        for (int i = 0; i < s.length(); i++) {
            int t = s.charAt(i) - 'a';
            if (curPos.next[t] == null) {
                curPos.next[t] = new BeforeTrie();
            }
            curPos = curPos.next[t];
        }
        curPos.isEnd = true;
    }
}


class Solution2 {
    public int respace(String[] dictionary, String sentence) {
        int n = sentence.length();
        int[] dp = new int[n + 1];
        Arrays.fill(dp, n);
        dp[0] = 0;
        Set<String> set = new HashSet(Arrays.asList(dictionary));
        for (int i = 0; i < n; i++) {
            dp[i + 1] = dp[i] + 1;
            for (int j = i; j >= 0; j--) {
                String substring = sentence.substring(j, i + 1);
                if (set.contains(substring)) {
                    dp[i + 1] = Math.min(dp[i + 1], dp[j]);
                }
            }
        }
        return dp[n];
    }
}
```
