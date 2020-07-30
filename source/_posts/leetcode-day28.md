---
title: leetcode-day28
date: 2020-07-20 09:53:32
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

![图片](http://api.mtyqx.cn/api/random.php?84)
<!-- more -->

### 题目[895.最大频率栈](https://leetcode-cn.com/problems/maximum-frequency-stack/)

实现 FreqStack，模拟类似栈的数据结构的操作的一个类。

FreqStack 有两个函数：

1. push(int x)，将整数 x 推入栈中。
2. pop()，它移除并返回栈中出现最频繁的元素。
    - 如果最频繁的元素不只一个，则移除并返回最接近栈顶的元素。

#### 解题思想

思路一：利用最大堆
思路二：维护最大的频率的栈

#### 代码

```java
class Peer {
    public int freq;
    public int loc;
    public int value;

    public Peer(int freq, int loc, int value) {
        this.freq = freq;
        this.loc = loc;
        this.value = value;
    }
}


class FreqStack {
    private PriorityQueue<Peer> stack;
    private Integer total;
    private Map<Integer, Integer> map;

    public FreqStack() {
        stack = new PriorityQueue<>(new Comparator<Peer>() {
            @Override
            public int compare(Peer p1, Peer p2) {
                if (p2.freq > p1.freq) {
                    return 1;
                } else if (p2.freq < p1.freq) {
                    return -1;
                } else return Integer.compare(p2.loc, p1.loc);
            }
        });
        total = 0;
        map = new HashMap<>();
    }

    public void push(int x) {
        int freq = map.getOrDefault(x, 0);
        Peer p = new Peer(freq, total, x);
        map.put(x,freq+1);
        total += 1;
        stack.offer(p);
    }

    public int pop() {
        Peer p = stack.poll();
        map.put(p.value, map.get(p.value) - 1);
        return p.value;
    }
}
```


```java
class FreqStack {
    Map<Integer, Integer> freq;
    Map<Integer, Stack<Integer>> group;
    int maxfreq;

    public FreqStack() {
        freq = new HashMap();
        group = new HashMap();
        maxfreq = 0;
    }

    public void push(int x) {
        int f = freq.getOrDefault(x, 0) + 1;
        freq.put(x, f);
        if (f > maxfreq)
            maxfreq = f;

        group.computeIfAbsent(f, z-> new Stack()).push(x);
    }

    public int pop() {
        int x = group.get(maxfreq).pop();
        freq.put(x, freq.get(x) - 1);
        if (group.get(maxfreq).size() == 0)
            maxfreq--;
        return x;
    }
}
```


### 题目[95.不同的二叉搜索树 II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

给定一个整数 n，生成所有由 1 ... n 为节点所组成的 二叉搜索树 。

#### 测试用例
输入：3
输出：
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]

#### 解题思路
dfs遍历，遍历返回的结果为以当前i为根节点的全部可能的子树，这些可能的子树就是由左右子树的排列构成的集合

#### 代码
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {
    }

    TreeNode(int val) {
        this.val = val;
    }

    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

class Solution {
    public List<TreeNode> generateTrees(int n) {
        if (n == 0) {
            return new LinkedList<>();
        }
        return generate(1, n);
    }

    public List<TreeNode> generate(int left, int right) {
        List<TreeNode> trees = new LinkedList<>();
        if (left > right) {
            trees.add(null);
            return trees;
        }
        for (int i = left; i <= right; i++) {
            List<TreeNode> leftTree = generate(left, i - 1);
            List<TreeNode> rightTree = generate(i + 1, right);
            for (TreeNode treeNode : leftTree) {
                for (TreeNode node : rightTree) {
                    TreeNode curTree = new TreeNode(i);
                    curTree.left = treeNode;
                    curTree.right = node;
                    trees.add(curTree);
                }
            }
        }
        return trees;
    }
}
```

### 题目[167.两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)
给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

说明:

返回的下标值（index1 和 index2）不是从零开始的。
你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

#### 测试用例
输入: numbers = [2, 7, 11, 15], target = 9
输出: [1,2]

#### 解题思路
利用有序列表的性质，采用左右双指针进行寻找

#### 代码
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[]{left + 1, right + 1};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{-1, -1};
    }
}
```

### 题目[312.戳气球](https://leetcode-cn.com/problems/burst-balloons/)
有 n 个气球，编号为0 到 n-1，每个气球上都标有一个数字，这些数字存在数组 nums 中。

现在要求你戳破所有的气球。如果你戳破气球 i ，就可以获得 nums[left] * nums[i] * nums[right] 个硬币。 这里的 left 和 right 代表和 i 相邻的两个气球的序号。注意当你戳破了气球 i 后，气球 left 和气球 right 就变成了相邻的气球。

求所能获得硬币的最大数量。

说明:

你可以假设 nums[-1] = nums[n] = 1，但注意它们不是真实存在的所以并不能被戳破。
0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100

#### 测试用例
输入: [3,1,5,8]
输出: 167 

#### 解题思路
逆向思维，戳破气球反过相当于在某个位置插入一个气球，定义dp状态为dp[i][j]为在开区间(i,j)插入一个气球能够得到的最多硬币，
dp[i][j] = max(val[i]*val[k]*val[j]+dp[i][k]+dp[k][j],dp[i][j])  i<j,k为插入位置，对于一个固定的i，j，需要遍历所有的k
dp[i][j] = 0  i>=j
#### 代码
```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n + 2][n + 2];
        int[] val = new int[n + 2];
        val[0] = val[n + 1] = 1;
        System.arraycopy(nums, 0, val, 1, n);
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 2; j <= n + 1; j++) {
                for (int k = i + 1; k < j; k++) {
                    int sum = val[i] * val[j] * val[k] + dp[i][k] + dp[k][j];
                    dp[i][j] = Math.max(dp[i][j], sum);
                }
            }
        }
        return dp[0][n + 1];
    }
}
```

### 题目[97.交错字符串](https://leetcode-cn.com/problems/interleaving-string/)
给定三个字符串 s1, s2, s3, 验证 s3 是否是由 s1 和 s2 交错组成的。
#### 测试用例
输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
输出：true

输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
输出：false

#### 解题思路
dp[i][j]表示a字符串位置为i，b字符串位置为j，target字符串位置为i+j-1能否匹配

#### 代码

```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int n = s1.length(), m = s2.length(), t = s3.length();

        if (n + m != t) {
            return false;
        }

        boolean[][] f = new boolean[n + 1][m + 1];

        f[0][0] = true;
        for (int i = 0; i <= n; ++i) {
            for (int j = 0; j <= m; ++j) {
                int p = i + j - 1;
                if (i > 0) {
                    f[i][j] = f[i][j] || (f[i - 1][j] && s1.charAt(i - 1) == s3.charAt(p));
                }
                if (j > 0) {
                    f[i][j] = f[i][j] || (f[i][j - 1] && s2.charAt(j - 1) == s3.charAt(p));
                }
            }
        }

        return f[n][m];
    }
}
```