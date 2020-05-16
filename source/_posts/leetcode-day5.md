---
title: leetcode-day5
date: 2020-05-16 11:00:02
tags:
- leetcode
- 算法
- python
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?x)
<!-- more -->

### 题目25[K个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)
给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。
k 是一个正整数，它的值小于或等于链表的长度。
如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

#### 用例
给你这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5

#### 解题思路
1. 可以利用一个栈实现节点的存储和调用
2. 空间O(1)的解法

#### 代码
```python

class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        if k==1 or not head or not head.next:
            return head
        record=cursor=ListNode(0)
        cursor.next=head
        while head:
            n=k
            end=head
            load=[]
            while n>0:
                load.append(end)
                end=end.next
                n -= 1
                if not end and n!=0:
                    return record.next
            while load and n==0:
                node=load.pop(-1)
                cursor.next=node
                cursor=cursor.next
            cursor.next=end
            head=end
        return record.next

class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        # 所谓的颠翻转其实可以理解为，规定指向下一轮k个节点的指针h，将后面顺序遍历到的k个节点依次放到h.next，这样就可以达到翻转的效果

        # 返回第k个节点，作为下一次翻转的头节点h
        def pair_rev(h) -> ListNode:
            # 记下此段的第一个节点记为f，需要前置的节点是f.next(即节点g)
            f = h.next
            for _ in range(k - 1):
                g = f.next
                f.next = g.next
                g.next = h.next
                h.next = g
            return f

        # 得到链表的长度，进而得到需要翻转的次数
        flag, n = head, 0
        while flag:
            flag = flag.next
            n += 1
        times = n // k

        # 新建一个节点指向链表
        a = ListNode(-1)
        a.next = head
        # 执行times次
        for _ in range(times):
            h0 = pair_rev(a)
        return a.next



class Solution:
    # 翻转一个子链表，并且返回新的头与尾
    def reverse(self, head: ListNode, tail: ListNode):
        prev = tail.next
        p = head
        while prev != tail:
            nex = p.next
            p.next = prev
            prev = p
            p = nex
        return tail, head

    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        hair = ListNode(0)
        hair.next = head
        pre = hair

        while head:
            tail = pre
            # 查看剩余部分长度是否大于等于 k
            for i in range(k):
                tail = tail.next
                if not tail:
                    return hair.next
            nex = tail.next
            head, tail = self.reverse(head, tail)
            # 把子链表重新接回原链表
            pre.next = head
            tail.next = nex
            pre = tail
            head = tail.next

        return hair.next

```

### 464[我能赢吗](https://leetcode-cn.com/problems/can-i-win/)
在 "100 game" 这个游戏中，两名玩家轮流选择从 1 到 10 的任意整数，累计整数和，先使得累
计整数和达到 100 的玩家，即为胜者。
如果我们将游戏规则改为 “玩家不能重复使用整数” 呢？例如，两个玩家可以轮流从公共整数池
中抽取从 1 到 15 的整数（不放回），直到累计整数和>= 100。
给定一个整数 maxChoosableInteger （整数池中可选择的最大数）和另一个整数desiredTotal
（累计和），判断先出手的玩家是否能稳赢（假设两位玩家游戏时都表现最佳）？
你可以假设 maxChoosableInteger 不会大于 20， desiredTotal 不会大于 300。

#### 解题思路
dfs遍历，并用状态压缩求解

#### 代码
```python
class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        import functools
        if desiredTotal <= maxChoosableInteger: return True
        if sum(range(maxChoosableInteger + 1)) < desiredTotal: return False

        @functools.lru_cache(None)
        def dfs(used, desiredTotal):
            for i in range(maxChoosableInteger):
                cur = 1 << i
                if cur & used == 0:
                    if desiredTotal <= i + 1 or not dfs(cur | used, desiredTotal - i - 1):
                        return True
            return False

        return dfs(0, desiredTotal)
```
### 467[环绕字符串中唯一的子字符串](https://leetcode-cn.com/problems/unique-substrings-in-wraparound-string/)
把字符串 s 看作是“abcdefghijklmnopqrstuvwxyz”的无限环绕字符串，所以 s 看起来是这样的："...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....". 
现在我们有了另一个字符串 p 。你需要的是找出 s 中有多少个唯一的 p 的非空子串，尤其是当你的输入是字符串 p ，你需要输出字符串 s 中 p 的不同的非空子串的数目。 
注意: p 仅由小写的英文字母组成，p 的大小可能超过 10000。

#### 思路
检验字符串中的连续递增子串，注意是循环的,存储下每个字母作为结尾时的最大长度，因为最长的必然包含较短的

#### 代码
```python
class Solution:
    def findSubstringInWraproundString(self, p: str) -> int:
        if not p:
            return 0
        hashmap=[0]*26
        hashmap[ord(p[0])-ord("a")]=1
        prelen=1
        for i in range(1,len(p)):
            gap=ord(p[i])-ord(p[i-1])
            if gap==1 or gap==-25:
                prelen+=1
            else:
                prelen=1
            hashmap[ord(p[i])-ord("a")]=max(hashmap[ord(p[i])-ord("a")],prelen)
        return sum(hashmap)
```