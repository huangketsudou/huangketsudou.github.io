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


### 题目[315.计算右侧小于当前元素的个数](https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self/)
给定一个整数数组 nums，按要求返回一个新数组 counts。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。


#### 用例
输入: [5,2,6,1]
输出: [2,1,1,0] 

#### 解题思路
思路一：归并排序，利用归并排序来求取数组的逆序对
思路二：树状数组，可以参考该博客——[树状数组](https://blog.csdn.net/Yaokai_AssultMaster/article/details/79492190)

树状数组适用于求解与前缀和相关的问题

#### 代码

```java
class Solution {
    private int[] c;
    private int[] a;

    public List<Integer> countSmaller(int[] nums) {
        List<Integer> resultList = new ArrayList<Integer>();
        discretization(nums);
        init(nums.length + 5);
        for (int i = nums.length - 1; i >= 0; --i) {
            int id = getId(nums[i]);
            resultList.add(query(id - 1));
            update(id);
        }
        Collections.reverse(resultList);
        return resultList;
    }

    private void init(int length) {
        c = new int[length];
        Arrays.fill(c, 0);
    }

    private int lowBit(int x) {
        return x & (-x);
    }

    private void update(int pos) {
        while (pos < c.length) {
            c[pos] += 1;
            pos += lowBit(pos);
        }
    }

    private int query(int pos) {
        int ret = 0;
        while (pos > 0) {
            ret += c[pos];
            pos -= lowBit(pos);
        }

        return ret;
    }

    private void discretization(int[] nums) {
        Set<Integer> set = new HashSet<Integer>();
        for (int num : nums) {
            set.add(num);
        }
        int size = set.size();
        a = new int[size];
        int index = 0;
        for (int num : set) {
            a[index++] = num;
        }
        Arrays.sort(a);
    }

    private int getId(int x) {
        return Arrays.binarySearch(a, x) + 1;
    }
}


class Solution3 {

    private int[] index;
    private int[] tmp;
    private int[] counter;

    public List<Integer> countSmaller(int[] nums) {
        List<Integer> result = new ArrayList<>();
        int n = nums.length;
        if (n==0){
            return result;
        }
        index = new int[n];
        tmp = new int[n];
        counter = new int[n];
        for (int i = 0; i < n; i++) {
            index[i] = i;
        }
        mergeAndSort(nums, 0, n - 1);
        for (int c : counter) {
            result.add(c);
        }
        return result;
    }

    public void mergeAndSort(int[] nums, int left, int right) {
        if (left == right) {
            return;
        }
        int mid = left + (right - left) / 2;
        mergeAndSort(nums, left, mid);
        mergeAndSort(nums, mid + 1, right);
        if (nums[index[mid]] > nums[index[mid + 1]]) {
            mergeTwoArray(nums, left, mid, right);
        }
    }

    public void mergeTwoArray(int[] nums, int left, int mid, int right) {
        for (int i = left; i <= right; i++) {
            tmp[i] = index[i];
        }
        int i = left;
        int j = mid + 1;
        for (int k = left; k <= right; k++) {
            if (i > mid) {
                index[k] = tmp[j];
                j++;
            } else if (j > right) {
                index[k] = tmp[i];
                i++;
                counter[index[k]] += (right - mid);
            } else if (nums[tmp[i]] <= nums[tmp[j]]) {
                index[k] = tmp[i];
                i++;
                counter[index[k]] += (j - mid - 1);
            } else {
                index[k] = tmp[j];
                j++;
            }
        }

    }
}
```

### 题目[785.判断二分图](https://leetcode-cn.com/problems/is-graph-bipartite/)
给定一个无向图graph，当这个图为二分图时返回true。

如果我们能将一个图的节点集合分割成两个独立的子集A和B，并使图中的每一条边的两个节点一个来自A集合，一个来自B集合，我们就将这个图称为二分图。

graph将会以邻接表方式给出，graph[i]表示图中与节点i相连的所有节点。每个节点都是一个在0到graph.length-1之间的整数。这图中没有自环和平行边： graph[i] 中不存在i，并且graph[i]中没有重复的值。

#### 用例
输入: [[1,3], [0,2], [1,3], [0,2]]
输出: true

输入: [[1,2,3], [0,2], [0,1,3], [0,2]]
输出: false

#### 解题思路
对图中的节点进行分组，一条边上的两个点要被分到不同的组，利用bfs算法进行分析，选择当前点，观察其分组，
如果还没有被分组，就分到组1，如果被分了组，就观察下个点，将该点的对面的点p2分到组2，并按照bfs算法分析
p2的对立点，如果其对立已经被分组，而且与p2同组，就返回false

代码中提供一个并查集的实现
#### 代码

```java
class Solution {

    private final int UNGROUPED = 0;
    private final int G1 = 1;
    private final int G2 = 2;

    public boolean isBipartite(int[][] graph) {
        int length = graph.length;
        int[] group = new int[length];
        for (int i = 0; i < length; i++) {
            if (group[i] == UNGROUPED) {
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                group[i] = G1;
                while (!queue.isEmpty()) {
                    int node = queue.poll();
                    int opGroup = group[node] == G1 ? G2 : G1;
                    for (int j : graph[node]) {
                        if (group[j]==UNGROUPED){
                            group[j] = opGroup;
                            queue.offer(j);
                        }else if(group[j]!=opGroup){
                            return false;
                        }
                    }
                }
            }
        }
        return true;
    }
}
```

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        // 初始化并查集
        UnionFind uf = new UnionFind(graph.length);
        // 遍历每个顶点，将当前顶点的所有邻接点进行合并
        for (int i = 0; i < graph.length; i++) {
            int[] adjs = graph[i];
            for (int w: adjs) {
                // 若某个邻接点与当前顶点已经在一个集合中了，说明不是二分图，返回 false。
                if (uf.isConnected(i, w)) {
                    return false;
                }
                uf.union(adjs[0], w);
            }
        }
        return true;
    }
}

// 并查集
class UnionFind {
    int[] roots;
    public UnionFind(int n) {
        roots = new int[n]; 
        for (int i = 0; i < n; i++) {
            roots[i] = i;
        }
    }

    public int find(int i) {
        if (roots[i] == i) {
            return i;
        }
        return roots[i] = find(roots[i]);
    }

    // 判断 p 和 q 是否在同一个集合中
    public boolean isConnected(int p, int q) {
        return find(q) == find(p);
    }

    // 合并 p 和 q 到一个集合中
    public void union(int p, int q) {
        roots[find(p)] = find(q);
    }
}
```