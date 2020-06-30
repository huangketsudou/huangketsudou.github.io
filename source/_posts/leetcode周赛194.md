---
title: leetcode周赛194
date: 2020-06-30 15:27:03
tags:
- leetcode周赛
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

### 题目[1486.数组异或操作](https://leetcode-cn.com/problems/xor-operation-in-an-array/)
给你两个整数，n 和 start 。

数组 nums 定义为：nums[i] = start + 2*i（下标从 0 开始）且 n == nums.length 。

请返回 nums 中所有元素按位异或（XOR）后得到的结果。

#### 用例
输入：n = 5, start = 0
输出：8

输入：n = 4, start = 3
输出：8

输入：n = 1, start = 7
输出：7
#### 解题思路
思路一:按要求造出数据逐个计算，事件复杂度为O(1)

思路二:介绍一种O(1)的解法，首先对于xor计算需要了解xor计算的一些性质
1. x ^ x = 0
2. 0 ^ x = x
3. 2x ^ 2x+1 = 1
4. 异或计算满足结合率

题目的要求是计算(start) ^ (start +2) ^ (start +4) ^ (start + 6) ^ ... ^ (start + 2(n-1)),
该计算公式与性质三非常像，但是不同的是，计算的是递增2，因此可以将每一项都右移移位，可以得到性质三的公式，

(start/2) ^ (start/2+1) ^ (start/2+2)^...^(start/2+n-1)

对于异或计算而言，右移计算相当于将最后的计算结果也向右移动了一位，而最后的一位的结果是由n和start决定的，当
start是偶数时，最后的一位一定是0，start是奇数时，当n为偶数，那么最后一位为0，其他情况下，最后一位是1

对于公式：(start/2) ^ (start/2+1) ^ (start/2+2)^...^(start/2+n-1)，可以利用公式3，但是要讨论start/2的奇偶性情况
1. start/2为偶数，
    1. n为偶数，那么公式就是n/2个1进行异或
    2. n为奇数，那么公式就是n/2个1进行异或，再异或(start/2+n-1)
2. start/2为奇数，那么公式可以通过补充(start/2-1)^(start/2-1),就变成了情况1讨论的。

#### 代码

```java
class Solution {
    public int xorOperation(int n, int start) {
        int ans = 2 * xor(n, start / 2);//右移之后，乘2左移回去
        if ((n & start & 1) == 1) {
            ans++;
        }
        return ans;
    }

    public int xor(int n, int start) {
        if ((start & 1) == 1) return (start - 1) ^ core(n + 1, start - 1);
        else return core(n, start);
    }
    public int core(int n, int start) {
        if (n % 2 == 0) return n / 2 & 1;
        else return n / 2 & 1 ^ (start + n - 1);
    }
}
```

### 题目[1487.保证文件名唯一](https://leetcode-cn.com/problems/making-file-names-unique/)
给你一个长度为 n 的字符串数组 names 。你将会在文件系统中创建 n 个文件夹：在第 i 分钟，新建名为 names[i] 的文件夹。

由于两个文件 不能 共享相同的文件名，因此如果新建文件夹使用的文件名已经被占用，系统会以 (k) 的形式为新文件夹的文件名添加后缀，其中 k 是能保证文件名唯一的 最小正整数 。

返回长度为 n 的字符串数组，其中 ans[i] 是创建第 i 个文件夹时系统分配给该文件夹的实际名称。


#### 用例
```
输入：names = ["pes","fifa","gta","pes(2019)"]
输出：["pes","fifa","gta","pes(2019)"]

输入：names = ["gta","gta(1)","gta","avalon"]
输出：["gta","gta(1)","gta(2)","avalon"]

输入：names = ["onepiece","onepiece(1)","onepiece(2)","onepiece(3)","onepiece"]
输出：["onepiece","onepiece(1)","onepiece(2)","onepiece(3)","onepiece(4)"]
```

#### 解题思路
哈希表保存，计算可能的idx

#### 代码
```java
class Solution {
    public String[] getFolderNames(String[] names) {
        HashMap<String,Integer> map = new HashMap<>();
        List<String> answer = new ArrayList<>();
        for (String name : names) {
            if(map.containsKey(name)){
                int k = map.get(name);
                String newName = name;
                while (map.containsKey(newName)){
                    newName = name+"("+k+")";
                    k++;
                }
                answer.add(newName);
                map.put(name,k);
                map.put(newName,1);
            }else {
                answer.add(name);
                map.put(name,1);
            }
        }
        return answer.toArray(String[]::new);
    }
}
```


### 题目[1488.避免洪水泛滥](https://leetcode-cn.com/problems/avoid-flood-in-the-city/)
你的国家有无数个湖泊，所有湖泊一开始都是空的。当第 n 个湖泊下雨的时候，如果第 n 个湖泊是空的，那么它就会装满水，否则这个湖泊会发生洪水。你的目标是避免任意一个湖泊发生洪水。

给你一个整数数组 rains ，其中：

rains[i] > 0 表示第 i 天时，第 rains[i] 个湖泊会下雨。
rains[i] == 0 表示第 i 天没有湖泊会下雨，你可以选择 一个 湖泊并 抽干 这个湖泊的水。
请返回一个数组 ans ，满足：

ans.length == rains.length
如果 rains[i] > 0 ，那么ans[i] == -1 。
如果 rains[i] == 0 ，ans[i] 是你第 i 天选择抽干的湖泊。
如果有多种可行解，请返回它们中的 任意一个 。如果没办法阻止洪水，请返回一个 空的数组 。

请注意，如果你选择抽干一个装满水的湖泊，它会变成一个空的湖泊。但如果你选择抽干一个空的湖泊，那么将无事发生（详情请看示例 4）。

#### 用例
输入：rains = [1,2,3,4]
输出：[-1,-1,-1,-1]


输入：rains = [1,2,0,0,2,1]
输出：[-1,-1,2,1,-1,-1]


输入：rains = [1,2,0,1,2]
输出：[]


输入：rains = [69,0,0,0,69]
输出：[-1,69,1,1,-1]
#### 解题思路
1. 要求在两次下雨之间必须至少存在一天不下雨，
2. 记录数据中的晴天，并在遍历雨天的过程中，如果发现某一个湖之前已经下过雨了，那么就从晴天中找一天满足1要求的日子，抽掉这个湖的水。



#### 代码
```java
class Solution {
    public int[] avoidFlood(int[] rains) {
        int n = rains.length;
        HashMap<Integer, Integer> rained = new HashMap<>();
        ArrayList<Integer> sunny = new ArrayList<>();
        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            if (rains[i] == 0) {
                sunny.add(i);
            } else {
                result[i] = -1;
                int pool = rains[i];
                if (rained.containsKey(pool)) {
                    int last = rained.get(pool);
                    int id = bisect(last,sunny);
                    int sunnyDay = id>=sunny.size()?-1:sunny.get(id);
                    if (sunnyDay>last){
                        result[sunnyDay] = pool;
                        sunny.remove(Integer.valueOf(sunnyDay));//这里必须要自动装箱，原因在于remove(int index)表示的是，移除index位置上的数，而不是移除index这个数
                        rained.put(pool,i);
                    }else return new int[0];
                }else
                    rained.put(pool,i);
            }

        }
        for (Integer day : sunny) {
            result[day] = 1;
        }
        return result;
    }

    public int bisect(int target, ArrayList<Integer> A) {
        int lo = 0, hi = A.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (A.get(mid) >= target) hi = mid;//保留这个最大值
            else lo = mid + 1;//去掉最小值。
        }
        return lo;
    }
}
```

### 题目[1489.找到最小生成树里的关键边和伪关键边](https://leetcode-cn.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/)
给你一个 n 个点的带权无向连通图，节点编号为 0 到 n-1 ，同时还有一个数组 edges ，其中 edges[i] = [fromi, toi, weighti] 表示在 fromi 和 toi 节点之间有一条带权无向边。最小生成树 (MST) 是给定图中边的一个子集，它连接了所有节点且没有环，而且这些边的权值和最小。

请你找到给定图中最小生成树的所有关键边和伪关键边。如果从图中删去某条边，会导致最小生成树的权值和增加，那么我们就说它是一条关键边。伪关键边则是可能会出现在某些最小生成树中但不会出现在所有最小生成树中的边。

请注意，你可以分别以任意顺序返回关键边的下标和伪关键边的下标。

#### 用例
```
输入：n = 5, edges = [[0,1,1],[1,2,1],[2,3,2],[0,3,2],[0,4,3],[3,4,3],[1,4,6]]
输出：[[0,1],[2,3,4,5]]
```
#### 解题思路
跑一遍Kruskal算法得到最小生成树的路径和, 然后遍历所有边, 对于找关键边, 我们把该边剔除掉, 如果发现找到的最小生成树的路径变大了, 那么该边一定是关键边; 否则, 对于伪关键边, 我们把该边先事先加到并查集中, 然后进行最小生成树的查找, 如果最后发现找到的路径总和等于之前计算好的最下路径和, 那么说明该边是伪关键边(非关键边但是存在着某个最小生成树里面)。

#### 代码

```python
class UnionFind:
    def __init__(self, n):
        self.parents = list(range(n))
        self.rank = [1] * n

    def find(self, x):
        if self.parents[x] == x:
            return x
        y = self.find(self.parents[x])
        self.parents[x] = y
        return y

    def union(self, x1, x2):
        y1, y2 = self.find(x1), self.find(x2)
        if y1 == y2:
            return
        if self.rank[y1] <= self.rank[y2]:
            self.parents[y1] = y2
        else:
            self.parents[y2] = y1
        if self.rank[y1] == self.rank[y2]:
            self.rank[y2] += 1

class Solution:
    def kruskal(self, n, edges, skip_edge=None):
        uf = UnionFind(n)
        cost = edges_cnt = 0
        if skip_edge is not None:
            v1, v2, w = skip_edge
            cost, edges_cnt = w, 1
            uf.union(v1, v2)
        for v1, v2, w in edges:
            if uf.find(v1) == uf.find(v2):
                continue
            cost += w
            edges_cnt += 1
            uf.union(v1, v2)
            if edges_cnt == n - 1:
                break
        return cost if edges_cnt == n - 1 else float("inf")

    def findCriticalAndPseudoCriticalEdges(self, n: int, edges: List[List[int]]) -> List[List[int]]:
        indices = sorted(range(len(edges)), key=lambda x: edges[x][2])
        edges = [edges[x] for x in indices]
        min_cost = self.kruskal(n, edges)
        ans = [[], []]
        for i in range(len(edges)):
            skip_edge = edges[i]
            new_edges = edges[:i]+edges[i+1:]
            if self.kruskal(n, new_edges) > min_cost:
                ans[0].append(indices[i])
            elif self.kruskal(n, new_edges, skip_edge) == min_cost:
                ans[1].append(indices[i])
        return ans
```