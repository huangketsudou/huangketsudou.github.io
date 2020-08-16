---
title: leetcode周赛202
date: 2020-08-16 14:00:38
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

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->


### 题目[5185. 存在连续三个奇数的数组](https://leetcode-cn.com/problems/three-consecutive-odds/)
给你一个整数数组 arr，请你判断数组中是否存在连续三个元素都是奇数的情况：如果存在，请返回 true ；否则，返回 false 。

#### 用例
输入：arr = [2,6,4,1]
输出：false

输入：arr = [1,2,34,3,4,5,7,23,12]
输出：true

#### 解题思路
滑动窗口

#### 代码
```java
class Solution {
    public boolean threeConsecutiveOdds(int[] arr) {
        int count = 0;
        for (int i : arr) {
            if ((i & 1) == 1){
                count++;
            }else {
                count = 0;
            }
            if (count>=3){
                return true;
            }
        }
        return false;
    }
}
```


### 题目[5488. 使数组中所有元素相等的最小操作数](https://leetcode-cn.com/problems/minimum-operations-to-make-array-equal/)
存在一个长度为 n 的数组 arr ，其中 arr[i] = (2 * i) + 1 （ 0 <= i < n ）。

一次操作中，你可以选出两个下标，记作 x 和 y （ 0 <= x, y < n ）并使 arr[x] 减去 1 、arr[y] 加上 1 （即 arr[x] -=1 且 arr[y] += 1 ）。最终的目标是使数组中的所有元素都 相等 。题目测试用例将会 保证 ：在执行若干步操作后，数组中的所有元素最终可以全部相等。

给你一个整数 n，即数组的长度。请你返回使数组 arr 中所有元素相等所需的 最小操作数 。


#### 用例
输入：n = 3
输出：2
解释：arr = [1, 3, 5]

#### 解题思路
贪心，把后面的大的移动给小的就可以，直到两者相等

#### 代码
```java
class Solution {
    public int minOperations(int n) {
        int start = 1;
        int end = 2 * (n - 1) + 1;
        int diff = end - start;
        int ans = 0;
        while (diff>0){
            ans += diff;
            diff -= 4;
        }
        return ans/2;
    }
}
```

### 题目[5489. 两球之间的磁力](https://leetcode-cn.com/problems/magnetic-force-between-two-balls/)
在代号为 C-137 的地球上，Rick 发现如果他将两个球放在他新发明的篮子里，它们之间会形成特殊形式的磁力。Rick 有 n 个空的篮子，第 i 个篮子的位置在 position[i] ，Morty 想把 m 个球放到这些篮子里，使得任意两球间 最小磁力 最大。

已知两个球如果分别位于 x 和 y ，那么它们之间的磁力为 |x - y| 。

给你一个整数数组 position 和一个整数 m ，请你返回最大化的最小磁力。

#### 用例
输入：position = [1,2,3,4,7], m = 3
输出：3

输入：position = [5,4,3,2,1,1000000000], m = 2
输出：999999999

#### 解题思路
二分法的解题思路，这道题的题目要求各段之间的最小间隔最大，可以将间隔的可能值进行二分，查找能够满足要求的最小值，分割的间隔在[1,position[-1]-position[0]]之间

类似的题目：[410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)
,[LCP 12. 小张刷题计划](https://leetcode-cn.com/problems/xiao-zhang-shua-ti-ji-hua/)


动态规划的思路可以参考410里面的官方题解
#### 代码
```java
class Solution {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int n = position.length;
        int l = 1;
        int r = position[n - 1] - position[0];
        m -= 2;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            int left = 0, count = 0;
            for (int i = 1; i < n; i++) {
                if (position[n - 1] - position[i] < mid) {
                    break;
                }
                if (position[i] - position[left] >= mid) {
                    left = i;
                    count++;
                }
            }
            if (count >= m) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return r;
    }
}
```

### 题目[5490. 吃掉 N 个橘子的最少天数]()
厨房里总共有 n 个橘子，你决定每一天选择如下方式之一吃这些橘子：

吃掉一个橘子。
如果剩余橘子数 n 能被 2 整除，那么你可以吃掉 n/2 个橘子。
如果剩余橘子数 n 能被 3 整除，那么你可以吃掉 2*(n/3) 个橘子。
每天你只能从以上 3 种方案中选择一种方案。

请你返回吃掉所有 n 个橘子的最少天数。


#### 用例
输入：n = 10
输出：4

输入：n = 6
输出：3


输入：n = 1
输出：1
#### 解题思路
bfs遍历，关键在于剪枝，对于bfs来说，当前遍历到的长度，如果它是第一次被访问到，那么
这个遍历就是对于这个n的最短的遍历结果了，因此可以用一个set来记录遍历的结果

#### 代码

```java
class Solution {
    public int minDays(int n) {
        Set<Integer> visited = new HashSet<>();
        Queue<int[]> stack = new LinkedList<>();
        stack.offer(new int[]{n, 0});
        while (!stack.isEmpty()) {
            int[] pair = stack.poll();
            int left = pair[0], day = pair[1];
            if (left == 0) {
                return day;
            }
            if (!visited.contains(left - 1)) {
                stack.offer(new int[]{left - 1, day + 1});
                visited.add(left - 1);
            }
            if ((left & 1) != 1 && !visited.contains(left - (left / 2))) {
                stack.offer(new int[]{left - left / 2, day + 1});
                visited.add(left - left / 2);
            }
            if ((left % 3 == 0) && !visited.contains(left - 2 * (left / 3))) {
                stack.offer(new int[]{left - 2 * (left / 3), day + 1});
                visited.add(left - 2*(left / 3));
            }
        }
        return n;
    }
}
```
一段时间不刷，成绩越来越滑了