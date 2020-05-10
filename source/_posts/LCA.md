---
title: LCA——最近公共节点题目题解类型
date: 2020-05-10 10:23:59
tags:
    - leetcode
    - LCA
    - python 
    - 公共子节点
categories:
 - 程序人生
 - 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?8)
<!-- more -->



### 题目[leetcode141:环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)
给定一个链表，判断链表中是否有环。
为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

#### 用例
输入：head = \[3,2,0,-4\], pos = 1
输出：true

输入：head = \[1,2\], pos = 0
输出：true

#### 解题思路
利用快慢指针，如果两指针能相遇，且不为None，那么链表有环,当然也可以用哈希表存储

#### 代码

````python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: ListNode) -> bool:
        if not head or not head.next:return False
        slow=head.next
        fast=head.next.next
        while slow and fast:
            if slow==fast:
                return True
            slow=slow.next
            if fast.next:
                fast = fast.next.next
            else:
                break
        return False
````


### 题目[142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

说明：不允许修改给定的链表。

#### 解题思路
利用快慢指针进行寻找，如果两指针可以相遇且不为None,那么就有环，接着记录该相遇点，并从链表的
头节点开始同步前进，两点再次相遇时，节点就是环的入口
其证明可以参照[题解](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/huan-xing-lian-biao-ii-by-leetcode/)
也可以用哈希表记录
#### 代码


```python
class Solution(object):
    def getIntersect(self, head):
        tortoise = head
        hare = head

        # A fast pointer will either loop around a cycle and meet the slow
        # pointer or reach the `null` at the end of a non-cyclic list.
        while hare is not None and hare.next is not None:
            tortoise = tortoise.next
            hare = hare.next.next
            if tortoise == hare:
                return tortoise

        return None

    def detectCycle(self, head):
        if head is None:
            return None

        # If there is a cycle, the fast/slow pointers will intersect at some
        # node. Otherwise, there is no cycle, so we cannot find an e***ance to
        # a cycle.
        intersect = self.getIntersect(head)
        if intersect is None:
            return None

        # To find the e***ance to the cycle, we have two pointers traverse at
        # the same speed -- one from the front of the list, and the other from
        # the point of intersection.
        ptr1 = head
        ptr2 = intersect
        while ptr1 != ptr2:
            ptr1 = ptr1.next
            ptr2 = ptr2.next

        return ptr1

```

### 题目[160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)和[面试题52. 两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

输入两个链表，找出它们的第一个公共节点。


#### 解题思路-参照题解区
创建两个指针 pApA 和 pBpB，分别初始化为链表 A 和 B 的头结点。然后让它们向后逐结点遍历。
当 pApA 到达链表的尾部时，将它重定位到链表 B 的头结点 (你没看错，就是链表 B); 类似的，当 pBpB 到达链表的尾部时，将它重定位到链表 A 的头结点。
若在某一时刻 pApA 和 pBpB 相遇，则 pApA/pBpB 为相交结点。
想弄清楚为什么这样可行, 可以考虑以下两个链表: A={1,3,5,7,9,11} 和 B={2,4,9,11}，相交于结点 9。 由于 B.length (=4) < A.length (=6)，pBpB 比 pApA 少经过 22 个结点，会先到达尾部。将 pBpB 重定向到 A 的头结点，pApA 重定向到 B 的头结点后，pBpB 要比 pApA 多走 2 个结点。因此，它们会同时到达交点。
如果两个链表存在相交，它们末尾的结点必然相同。因此当 pApA/pBpB 到达链表结尾时，记录下链表 A/B 对应的元素。若最后元素不相同，则两个链表不相交。

也可以哈希表
#### 代码


```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        h1,h2=headA,headB
        while h1!=h2:
            h1=h1.next if h1 else headB
            h2=h2.next if h2 else headA
        return h1
```

### 题目[235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。
百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

#### 解题思路
利用好二叉搜索树左子树的值都小于根以及右子树的值都大于根的特性

#### 代码

```python
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        node = root
        while True:
            if node.val > p.val and node.val > q.val:
                node = node.left
            elif node.val < p.val and node.val < q.val:
                node = node.right
            else:
                return node
```


### 题目[236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”


#### 解题思路

问题实际可以转化为链表的公共节点问题

#### 代码

```python
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:

    def lowestCommonAncestor(self, root, p, q):

        stack=[root]
        parent={root:None}

        while p not in parent or q not in parent:
            node=stack.pop()
            if node.left:
                stack.append(node.left)
                parent[node.left]=node
            if node.right:
                stack.append(node.right)
                parent[node.right]=node
        ancestor=p
        path=[]
        while ancestor:
            path.append(ancestor)
            ancestor=parent[ancestor]

        while q not in path:
            q=parent[q]
        return q
```