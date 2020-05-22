---
title: leetcode-10
date: 2020-05-22 10:43:58
tags:
- leetcode
- 算法
- python
- 动态规划
- 构造二叉树
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?256)
<!-- more -->


### 题目[105从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
据一棵树的前序遍历与中序遍历构造二叉树。
注意:你可以假设树中没有重复的元素。


#### 用例
前序遍历 preorder = \[3,9,20,15,7]
中序遍历 inorder = \[9,3,15,20,7]


#### 解题思路
1. 利用递归，结合中序遍历与前序遍历地关系，前序遍历第一个节点为头节点，找出在中序遍历中的位置可以划分左右子树
最后的方法为O(N**2)，可以利用map把时间复杂度降到O(N)
2. 迭代法
    - 我们用一个栈和一个指针辅助进行二叉树的构造。初始时栈中存放了根节点（前序遍历的第一个节点），指针指向中序遍历的第一个节点；
    - 我们依次枚举前序遍历中除了第一个节点以外的每个节点。如果 index 恰好指向栈顶节点，那么我们不断地弹出栈顶节点并向右移动 
    index，并将当前节点作为最后一个弹出的节点的右儿子；如果 index 和栈顶节点不同，我们将当前节点作为栈顶节点的左儿子；
    - 无论是哪一种情况，我们最后都将当前的节点入栈。
    
迭代法来自[eetcode题解](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/cong-qian-xu-yu-zhong-xu-bian-li-xu-lie-gou-zao-9/)



#### 代码
```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    #这个用前序和中序合成
    #注意list.index是线性查找，最坏的情况退化到O(n**2),可以利用哈希表实现最坏O(n)
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not preorder:
            return None
        index=preorder[0]
        cur=inorder.index(index)
        root=TreeNode(index)
        root.left=self.buildTree(preorder[1:1+cur],inorder[:cur])
        root.right=self.buildTree(preorder[1+cur:],inorder[cur+1:])
        return root


class Solution:
    #迭代法计算思路
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not preorder:
            return None
        root=TreeNode(preorder[0])
        stack=[root]
        inorderIndex=0
        for i in range(1,len(preorder)):
            preorderVal=preorder[i]
            node=stack[-1]
            if node.val!=inorder[inorderIndex]:
                node.left=TreeNode(preorderVal)
                stack.append(node.left)
            else:
                while stack and stack[-1].val == inorder[inorderIndex]:
                    node=stack.pop()
                    inorderIndex+=1
                node.right=TreeNode(preorderVal)
                stack.append(node.right)
        return root
```


### 题目
根据一棵树的中序遍历与后序遍历构造二叉树。
注意:你可以假设树中没有重复的元素。

#### 用例
中序遍历 inorder = \[9,3,15,20,7]
后序遍历 postorder = \[9,15,7,20,3]

#### 解题思路
利用后序遍历与中序遍历的关系，后序遍历最后一个为根，可以通过中序遍历找到其左右子树


#### 代码
```python
class Solution3:
    #注意list.index是线性查找，最坏的情况退化到O(n**2),可以利用哈希表实现最坏O(n)
    #这个用中序和后序
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode:
        if not postorder:
            return None
        index=postorder[-1]
        cur=inorder.index(index)
        root=TreeNode(index)
        root.left=self.buildTree(inorder[:cur],postorder[:cur])
        root.right=self.buildTree(inorder[cur+1:],postorder[cur:-1])
        return root
```


### 题目
返回与给定的前序和后序遍历匹配的任何二叉树。
pre 和 post 遍历中的值是不同的正整数。

#### 用例
pre = \[1,2,4,5,3,6,7], post = \[4,5,2,6,7,3,1]

#### 解题思路
前序遍历第一个为根，后面紧跟的第一个数为其左子树根节点(如果没有左子树，则为右子树根节点)，可以在后序遍历中找到他的左右子树范围


#### 代码
```python
class Solution:
    def constructFromPrePost(self, pre: List[int], post: List[int]) -> TreeNode:
        if not pre:return None
        preval=pre[0]
        root=TreeNode(preval)
        if len(pre)!=1:
            nxt=pre[1]
            idx=post.index(nxt)
            root.left=self.constructFromPrePost(pre[1:1+idx+1],post[:idx+1])
            root.right=self.constructFromPrePost(pre[2+idx:],post[idx+1:-1])
        return root
```