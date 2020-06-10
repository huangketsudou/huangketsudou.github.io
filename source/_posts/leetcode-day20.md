---
title: leetcode-day20
date: 2020-06-10 20:40:01
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

![图片](http://api.mtyqx.cn/api/random.php?147)
<!-- more -->

### 题目[887.鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/)
你将获得 K 个鸡蛋，并可以使用一栋从 1 到 N  共有 N 层楼的建筑。

每个蛋的功能都是一样的，如果一个蛋碎了，你就不能再把它掉下去。

你知道存在楼层 F ，满足 0 <= F <= N 任何从高于 F 的楼层落下的鸡蛋都会碎，从 F 楼层或比它低的楼层落下的鸡蛋都不会破。

每次移动，你可以取一个鸡蛋（如果你有完整的鸡蛋）并把它从任一楼层 X 扔下（满足 1 <= X <= N）。

你的目标是确切地知道 F 的值是多少。

无论 F 的初始值如何，你确定 F 的值的最小移动次数是多少？


#### 用例
输入：K = 1, N = 2
输出：2

输入：K = 2, N = 6
输出：3

输入：K = 3, N = 14
输出：4
#### 解题思路
动态规划——对于一个鸡蛋，他从某一层x(总层数N)掉落具有两种可能性：
    1. 鸡蛋碎了，需要换一颗鸡蛋，从比较低层再试，此时低层需要试最多x-1次(仅剩一个鸡蛋)
    2. 鸡蛋没碎，可以用这个鸡蛋从高层再试，此时高层需要试最多N-x次(仅剩一个鸡蛋)
    3. 所以在某一层掉落，他需要试的次数为至少max(f(k-1,x-1),f(k,N-x)),k为剩下的鸡蛋数，f为计算次数的函数
    所以对于一个具有N层高的楼层，需要试的最少的次数为min(G(i)),i=1,2,...,N,G(i) 为从i层开始试所需要的最少的次数
    4. 对于G(i),G(i) = min(max(f(k-1,x-1),f(k,N-x))) x =1,2,...,i
    5. 常规的dp使用的时间为N*K*N,超时，所以用二分法去做求最小值的结果，为什么参考题解区——[鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/solution/ji-dan-diao-luo-by-leetcode-solution-2/)
    

#### 代码
```java
class Solution {
    public int superEggDrop(int K, int N) {
        int[][] dp = new int[K][N + 2];
        for (int i = 0; i < N; i++) {
            dp[0][i + 1] = i + 1;
        }
        for (int i = 1; i < K; i++) {
            for (int j = 1; j <= N; j++) {
//                int ans = Integer.MAX_VALUE;
//                for (int k = 1; k <= j; k++) {
//                    ans = Math.min(ans, Math.max(dp[i - 1][k - 1], dp[i][j - k]));
//                }
                int left = 1, right = j;
                while (left + 1 < right) {
                    int x = (left+right)/2;
                    int t1 = dp[i-1][x - 1];
                    int t2 = dp[i][j - x];
                    if (t1 < t2){
                        left = x;
                    }else if (t1>t2){
                        right = x;
                    }else left = right = x;
                }
                int ans = Math.min(Math.max(dp[i-1][left-1],dp[i][j - left]),Math.max(dp[i-1][right-1],dp[i][j - right]));
                dp[i][j] = ans + 1;
            }
        }
        System.out.println(Arrays.toString(dp[K - 1]));
        return dp[K - 1][N];
    }
}
```

```python
class Solution:
    def superEggDrop(self, K: int, N: int) -> int:
        memo = {}
        def dp(k, n):
            if (k, n) not in memo:
                if n == 0:
                    ans = 0
                elif k == 1:
                    ans = n
                else:
                    lo, hi = 1, n
                    # keep a gap of 2 X values to manually check later
                    while lo + 1 < hi:
                        x = (lo + hi) // 2
                        t1 = dp(k-1, x-1)
                        t2 = dp(k, n-x)

                        if t1 < t2:
                            lo = x
                        elif t1 > t2:
                            hi = x
                        else:
                            lo = hi = x

                    ans = 1 + min(max(dp(k-1, x-1), dp(k, n-x))
                                  for x in (lo, hi))

                memo[k, n] = ans
            return memo[k, n]

        return dp(K, N)
```

### 题目[9.回文数](https://leetcode-cn.com/problems/palindrome-number/)
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。
#### 用例
输入: 121
输出: true

输入: -121
输出: false

输入: 10
输出: false
#### 解题思路
比较数字的一般部分就可以了

#### 代码
```java
class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }
        int tmp = 0;
        while (tmp <= x) {
            int mod = x % 10;  
            tmp = 10 * tmp + mod;
            if (tmp == x || tmp == x/10){
                return true;
            }
            x /= 10;
        }
        return false;
    }
}
```

### 题目[126. 词接龙 II]()
给定两个单词（beginWord 和 endWord）和一个字典 wordList，找出所有从 beginWord 到 endWord 的最短转换序列。转换需遵循如下规则：

每次转换只能改变一个字母。转换后得到的单词必须是字典中的单词。

说明:
如果不存在这样的转换序列，返回一个空列表。
1. 所有单词具有相同的长度。
2. 所有单词只由小写字母组成。
3. 字典中不存在重复的单词。
4. 你可以假设 beginWord 和 endWord 是非空的，且二者不相同。

#### 用例
```
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出:
[
  ["hit","hot","dot","dog","cog"],
  ["hit","hot","lot","log","cog"]
]
```
#### 解题思路
bfs 遍历可以解决，重点是通过什么在两个节点之间建立联系，本题采用了将字符串中的一个字符替换为*，
如果两个字符串的一种换星组合相同，那么这两个字符串之间具有通路，

此外通过一个visited数组来保存对某一个字符串访问的时间(在某一条路径中出现的位置)，只要不超过这个位置就可以添加到路径

#### 代码
```java
class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        List<List<String>> result =new ArrayList<>();
        if (beginWord==null || endWord==null || wordList.size()==0 ||!wordList.contains(endWord)){
            return result;
        }
        HashMap<String,ArrayList<String>> graph = new HashMap<>();
        int length = beginWord.length();
        for (String s : wordList) {
            for (int i = 0; i < length; i++) {
                String tmp = s.substring(0,i)+"*"+s.substring(i+1);
                if (graph.containsKey(tmp)){
                    graph.get(tmp).add(s);
                }else {
                    graph.put(tmp,new ArrayList<String>(){{add(s);}});
                }
            }
        }
        Queue<List<String>> queue = new ArrayDeque<>();
        HashMap<String,Integer> visited = new HashMap<>();
        queue.add(new ArrayList<>(){{add(beginWord);}});
        visited.put(beginWord,1);
        boolean firstFound = true;
        int minlevel = 0;
        while (queue.size()!=0){
            List<String> currentPath = queue.poll();
            String finalWord = currentPath.get(currentPath.size()-1);
            for (int i = 0; i < length; i++) {
                String intermediate = finalWord.substring(0,i)+"*"+finalWord.substring(i+1);
                if (!graph.containsKey(intermediate)) continue;
                for (String s : graph.get(intermediate)) {
                    List<String> tmpPath = new ArrayList<>(currentPath);
                    if (s.equals(endWord) && !tmpPath.contains(s)){
                        if (firstFound){
                            tmpPath.add(s);
                            firstFound = false;
                            minlevel = tmpPath.size();
                            result.add(tmpPath);
                        }else {
                            if (tmpPath.size()+1 > minlevel){
                                return result;
                            }else {
                                tmpPath.add(s);
                                result.add(tmpPath);
                            }
                        }
                        continue;
                    }
                    if (!visited.containsKey(s) || visited.get(s)>=tmpPath.size()+1){
                        visited.put(s,1+tmpPath.size());
                        tmpPath.add(s);
                        queue.add(tmpPath);
                    }
                }
            }
        }
        return result;
    }
}
```


### 题目[面试题46.把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)
给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。
请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。


#### 用例

输入: 12258
输出: 5

#### 解题思路
dp或者dfs都行

#### 代码
```java
class Solution {
    public int translateNum(int num) {
        String number = String.valueOf(num);
        return dfs(number,0);
    }

    public int dfs(String number, int index) {
        if (index == number.length()) {
            return 1;
        }
        int res = 0;
        res += dfs(number, index + 1);
        if (index < number.length() - 1 && number.charAt(index) != '0' && Integer.parseInt(number.substring(index,index+2))<26) {
            res += dfs(number, index + 2);
        }
        return res;
    }
}
```