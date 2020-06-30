---
title: leetcode-day25
date: 2020-06-30 11:52:08
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

### 题目[面试题 02.01. 移除重复节点](https://leetcode-cn.com/problems/remove-duplicate-node-lcci/)
编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

#### 用例
```
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
```
#### 解题思路
思路一：利用哈希表记录出现过的数
思路二：如果不用哈希表，那么在添加一个节点时，遍历已经添加过的节点，确保该节点没有在其中再添加

#### 代码
```java
class ListNode {
    int val;
    ListNode next;

    ListNode(int x) {
        val = x;
    }
}

class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        Set<Integer> visited = new HashSet<>();
        ListNode record = new ListNode(-1);
        ListNode prev = record;
        while (head != null) {
            if (!visited.contains(head.val)) {
                prev.next = head;
                prev = head;
                visited.add(head.val);
            }
            head = head.next;
        }
        prev.next = null;
        return record.next;
    }
}


class Solution2 {
    public ListNode removeDuplicateNodes(ListNode head) {
        ListNode ob = head;
        while (ob != null) {
            ListNode oc = ob;
            while (oc.next != null) {
                if (oc.next.val == ob.val) {
                    oc.next = oc.next.next;
                } else {
                    oc = oc.next;
                }
            }
            ob = ob.next;
        }
        return head;
    }
}

```

### 题目[139.单词拆分](https://leetcode-cn.com/problems/word-break/)
给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：
- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。
#### 用例
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true

输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true

#### 解题思路
利用动态规划。
1. 对于字符串s，如果它能够用字典里的字符串来组成，那么其如果分为s[:i]和s[i:j]两部分，s[:j]能够用字典表示，则有s[i:j]在字典中，且s[:i]能用字典表示

#### 代码
```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        Set<String> set = new HashSet<>(wordDict);
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (set.contains(s.substring(j, i))) {
                    dp[i] = dp[i] || dp[j];
                }
            }
        }
        return dp[n];
    }
}
```

### 题目[16.最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

#### 用例

输入：nums = [-1,2,1,-4], target = 1
输出：2
#### 解题思路
三数和问题：排序之后利用双指针进行选择，三数和题目的常用套路

#### 代码
```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int ans = 1000000;
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int tmp = nums[i] + nums[left] + nums[right];
                System.out.println(tmp);
                System.out.println(ans);
                if (Math.abs(tmp - target) < Math.abs(ans - target)) {
                    ans = tmp;
                }
                if (tmp > target) {
                    while (left < right && nums[right - 1] == nums[right]) {
                        right--;
                    }
                    right--;
                } else if (tmp < target) {
                    while (left < right && nums[left + 1] == nums[left]) {
                        left++;
                    }
                    left++;
                } else
                    return ans;
            }
        }
        return ans;
    }
}
```

### 题目[41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)
给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。

#### 用例
输入: [1,2,0]
输出: 3

输入: [3,4,-1,1]
输出: 2

输入: [7,8,9,11,12]
输出: 1
#### 解题思路
思路1：采用哈希表记录数组中的数
思路2：原地置换
1. 对于题目中的条件，可以直到，当所有的数字都存在于数组中时，nums[i]==i+1
2. 基于上面的条件，可以将数字进行置换，将数字放到它应该去的位置，当数字被放到应该去的位置之后，原来位置
会得到一个新的数，该数如果位置不对，还要接着放，注意如果nums[i]==nums[nums[i]-1],会造成死循环，需要排除


#### 代码
```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        int i = 0;
        while (i < n) {
            while (nums[i] != i + 1 && nums[i]>0 && nums[i] <= n && nums[i]!=nums[nums[i]-1]){
                int tmp = nums[i];
                nums[i] = nums[nums[i]-1];
                nums[tmp-1] = tmp;
            }
            i++;
        }
        for (i = 0; i < n; i++) {
            if (nums[i]!=i+1){
                return i+1;
            }
        }
        return n+1;
    }
}
```



### 题目[209.长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)
给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的连续子数组，并返回其长度。如果不存在符合条件的连续子数组，返回 0。


#### 用例
输入：s = 7, nums = [2,3,1,2,4,3]
输出：2

#### 解题思路
双指针，或者前缀和

#### 代码
```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int length = nums.length;
        int ans = Integer.MAX_VALUE;
        int left = 0, right = 0;
        int summary = 0;
        while (right < length) {
            summary += nums[right];
            while (summary > s) {
                ans = Math.min(ans, right - left + 1);
                summary -= nums[left++];
            }
            right++;
        }
        return ans != Integer.MAX_VALUE ? ans : 0;
    }
}

class Solution2 {
    public int minSubArrayLen(int s, int[] nums) {
        int[] ConSum = new int[nums.length+1];
        for (int i = 1; i <= nums.length; i++) {
            ConSum[i] = ConSum[i - 1] + nums[i-1];
        }
        int left = 0;
        int ans = Integer.MAX_VALUE;
        for (int j = 1; j <= nums.length; j++) {
            while (ConSum[j] - ConSum[left] >= s) {
                ans = Math.min(ans, j - left);
                left++;
            }
        }
        return ans != Integer.MAX_VALUE ? ans : 0;
    }
}
```

### 题目[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)
在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

#### 用例
```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```

#### 解题思路
思路一：最小堆


#### 代码
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        Queue<Integer> heap = new PriorityQueue<>(k + 1, (a, b) -> (a - b));
        for (int i = 0; i < k; i++) {
            heap.add(nums[i]);
        }
        for (int j = k; j < nums.length; j++) {
            heap.add(nums[j]);
            heap.poll();
        }
        return heap.peek();
    }
}

class Solution2 {
    Random random = new Random();

    public int findKthLargest(int[] nums, int k) {
        int answer = quickSelect(nums, 0, nums.length - 1, nums.length - k);
        System.out.println(Arrays.toString(nums));
        return answer;
    }

    public int quickSelect(int[] a, int l, int r, int index) {
        int q = randomPartition(a, l, r);
        if (q == index) {
            return a[q];
        } else {
            return q < index ? quickSelect(a, q + 1, r, index) : quickSelect(a, l, q - 1, index);
        }
    }

    public int randomPartition(int[] a, int l, int r) {
        int i = random.nextInt(r - l + 1) + l;
        swap(a, i, r);
        return partition(a, l, r);
    }

    public int partition(int[] a, int l, int r) {
        int x = a[r], i = l - 1;
        for (int j = l; j < r; ++j) {
            if (a[j] <= x) {
                swap(a, ++i, j);//这里是和i进行交换，而不是j
            }
        }
        swap(a, i + 1, r);
        return i + 1;
    }

    public void swap(int[] a, int i, int j) {
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

### 题目[剑指 Offer 09.用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)
用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

#### 代码
```java
class CQueue {
    Deque<Integer> deque;
    Deque<Integer> SupportDeque;
    public CQueue() {
        deque = new ArrayDeque<>();
        SupportDeque = new ArrayDeque<>();
    }

    public void appendTail(int value) {
        deque.push(value);
    }

    public int deleteHead() {
        if(SupportDeque.isEmpty()){
            while (!deque.isEmpty()){
                SupportDeque.push(deque.pop());
            }
        }
        if (SupportDeque.isEmpty()){
            return -1;
        }else 
            return SupportDeque.pop();
    }
}
```


