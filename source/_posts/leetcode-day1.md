---
title: leetcode-day1
date: 2020-05-12 11:05:07
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

### 题目155[最小栈](https://leetcode-cn.com/problems/min-stack/)
设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

+ push(x) —— 将元素 x 推入栈中。
+ pop() —— 删除栈顶的元素。
+ top() —— 获取栈顶元素。
+ getMin() —— 检索栈中的最小元素。

#### 思路
辅助栈存最小值

#### 代码
```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack=[]
        self.minstack=[]


    def push(self, x: int) -> None:
        self.stack.append(x)
        if len(self.minstack)==0 or x<self.minstack[-1]:
            self.minstack.append(x)
        else:
            self.minstack.append(self.minstack[-1])

    def pop(self) -> None:
        self.minstack.pop()
        return self.stack.pop()

    def top(self) -> int:
        if len(self.stack)!=0:
            return self.stack[-1]

    def getMin(self) -> int:
        if len(self.minstack)>0:
            return self.minstack[-1]



# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```



### 题目449[序列化和反序列化二叉搜索树](https://leetcode-cn.com/problems/serialize-and-deserialize-bst/)
序列化是将数据结构或对象转换为一系列位的过程，以便它可以存储在文件或内存缓冲区中，或通过网络连接链路传输，以便稍后在同一个或另一个计算机环境中重建。
设计一个算法来序列化和反序列化二叉搜索树。 对序列化/反序列化算法的工作方式没有限制。 您只需确保二叉搜索树可以序列化为字符串，并且可以将该字符串反序列化为最初的二叉搜索树。
编码的字符串应尽可能紧凑。

#### 思路
递归调用


#### 代码
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Codec:

    def serialize(self, root: TreeNode):
        """Encodes a tree to a single string.
        """
        if not root: return []
        stack = [root]
        res = []
        while stack:
            curres = []
            child = []
            for node in stack:
                if node:
                    curres.append(node.val)
                    child.append(node.left)
                    child.append(node.right)
                else:
                    curres.append(None)
            res.extend(curres)
            stack = child
        return res

    def deserialize(self, data: str) -> TreeNode:
        """Decodes your encoded data to tree.
        """
        if not data: return None
        root = TreeNode(data[0])
        parent = [root]
        i = 1
        child = []
        while parent:

            for node in parent:
                if i < len(data) and data[i] is not None:
                    node.left = TreeNode(data[i])
                    child.append(node.left)
                i += 1
                if i < len(data) and data[i] is not None:
                    node.right = TreeNode(data[i])
                    child.append(node.right)
                i += 1
            parent=child
            child=[]
        return root
```

### 题目442[数组中重复的数据](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/)
给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。
找到所有出现两次的元素。


#### 基本思路
由于数组中的元素范围是1-n，因此可以调整数组元素位置，是元素值对应他的下标
，也可以用哈希表等方式
#### 代码

```python
class Solution:
    def findDuplicates(self, nums: List[int]) -> List[int]:
        n = len(nums)
        if n < 2: return []
        i = 0
        while i < n:
            while i+1!=nums[i]:
                if nums[nums[i]-1]==nums[i]:
                    break
                nums[nums[i]-1],nums[i]=nums[i],nums[nums[i]-1]
            i+=1
        res=[]
        for i,v in enumerate(nums):
            if i+1!=v:
                res.append(v)  
        return res
```

### 题目450[删除二叉搜索树](https://leetcode-cn.com/problems/delete-node-in-a-bst/)
#### 题目
给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点；
如果找到了，删除它。

#### 基本思路
官方的题解是通过对要删除的节点进行重新赋值后实现删除的方法，这里提供一种直接删除节点的代码实现

#### 查找结点
利用二叉搜索树的性质，结合二分查找可以将查找的过程降低到h，数的高度

#### 节点的删除
要在一个二叉树中删除一个节点，需要，就需要正确处理这个节点的子树如何与该节点的父节点进行连接的问题，我们以官方的一个测试用例来进行分析
用例为
`[8,0,31,null,6,28,45,1,7,25,30,32,49,null,4,null,null,9,26,29,null,null,42,47,null,2,5,null,12,null,27,null,null,41,43,46,48,null,3,null,null,10,19,null,null,33,null,null,44,null,null,null,null,null,null,null,11,18,20,null,37,null,null,null,null,14,null,null,22,36,38,13,15,21,24,34,null,null,39,null,null,null,16,null,null,23,null,null,35,null,40,null,17]
1`
他可视化后的部分结构如图所示：
![image.png](https://pic.leetcode-cn.com/33ac42089d550283fd2166e801dc89179a675d412d76e0afbec2da28f2082c8e-image.png)
可以看到我们要删除的节点就是1节点，它具有右子树，
我们对这个节点的连接情况进行讨论
1. 要删除的节点没有子节点，
2. 要删除的节点有右子树，左子树的有无不影响结果
3. 要删除的节点没有右子树，但有左子树

情况1，我们可以直接将这个节点置为None就可以解决问题
```
root=None
```
情况2，这种情况下，我们需要找到要删除的节点的下一个节点，该节点其实就是中序遍历下排在要删除节点之后的那一个节点（我们把这个节点记作child），找到这个节点的代码如下,parent用于记录child节点的父节点
```
parent, child = root, root.right
while child.left is not None:
    parent = child
    child = child.left
```
再分两种情况，
1. parent节点不是root节点，现在通过这个程序可以找到1的下一个节点是2，且`parent!=root`此时用2来替换1，也就是通过
```
child.left=root.left
child.right=root.right
root=child
```
将root变更为child并把root的左右子树给child实现，但是注意2它具有一个子节点3，这个3在中序遍历中出现于2之后，4之前，也就是应将2的右子树连接到2的父节点4的左子树上，完整的删除代码如下:
```
parent.left=child.right
child.left=root.left
child.right=root.right
root=child
```
2. 如果parent节点就是root节点，也就是root的下一个中序遍历经过的节点就是他的右孩子，那么此时右孩子没有左子树，将root的左子树连接到child，并令root=child，代码
```
child.left = root.left
root = child
```
情况3，这种情况下，我们应该寻找要删除节点的上一个节点，也就是中序遍历中排在要删除节点之前的一个节点，找到该节点的代码如下,假设此时要删除25，那么通过这段代码找到他的上一个节点是24，24有一个左节点23
```
parent, child = root, root.left
while child.right is not None:
    parent = child
    child = child.right
```
也分两种情况讨论
1. parent节点不是root节点，现在通过这个程序可以找到25的上一个节点是24，且`parent!=root`此时用24来替换25，也就是通过
```
child.left = root.left
child.right = root.right
root = child
```
将root变更为child并把root的左右子树给child实现，但是注意24它具有一个左子节点23，这个3在中序遍历中出现于24之前，22之前，也就是应将24的左子树连接到24的父节点22的右子树上，完整的删除代码如下:
```
parent.right=child.left
child.left = root.left
child.right = root.right
root = child
```
2. 如果parent节点就是root节点，也就是root的上一个中序遍历经过的节点就是他的左孩子，那么此时左孩子没有右子树，将root的右子树连接到child，并令root=child，代码
```
child.right = root.right
root = child
```

### 代码

```python3
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
        if root is None: return root
        if root.val > key:
            root.left = self.deleteNode(root.left, key)
        elif root.val <key:
            root.right = self.deleteNode(root.right, key)
        else:
            if root.left:
                parent, child = root, root.left
                while child.right is not None:
                    parent = child
                    child = child.right
                if parent != root:
                    parent.right=child.left
                    child.left = root.left
                    child.right = root.right
                    root = child
                else:
                    child.right = root.right
                    root = child
            elif root.right:
                parent, child = root, root.right
                while child.left is not None:
                    parent = child
                    child = child.left
                if parent != root:
                    parent.left = child.right
                    child.right = root.right
                    child.left = root.left
                    root = child
                else:
                    child.left = root.left
                    root = child
            else:
                root = None
        return root
```
