---
title: leetcode-day11
date: 2020-05-23 13:11:19
tags:
- leetcode
- 算法
- python
- 动态规划
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?569)
<!-- more -->
### 题目[76.最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)
给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字符的最小子串。

#### 用例
输入: S = "ADOBECODEBANC", T = "ABC"

输出: "BANC"
#### 解题思路
滑动窗口求解问题，利用哈希表记录窗口中各种字母的数字，利用formed记录窗口中的字母是否满足构成所需的条件(数目),当满足时左侧指针移动缩小指针

#### 代码
```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        n = len(s)
        if n == 0:
            return ''
        l, r = 0, 0
        count = Counter(t)
        length = len(count)
        windowsdict = defaultdict(int)
        formed = 0
        start = 0
        minlength = float('inf')
        while r < n:
            if s[r] in count:
                windowsdict[s[r]] += 1
                if windowsdict[s[r]] == count[s[r]]:
                    formed += 1
            r += 1
            while l < r and formed == length:
                if s[l] in count:
                    if r - l < minlength:
                        minlength = r - l
                        start = l
                    windowsdict[s[l]] -= 1
                    if windowsdict[s[l]] < count[s[l]]:
                        formed -= 1
                l += 1
        return s[start:start + minlength] if minlength != float('inf') else ''
```

### 题目[495.提莫攻击](https://leetcode-cn.com/problems/teemo-attacking/)
在《英雄联盟》的世界中，有一个叫 “提莫” 的英雄，他的攻击可以让敌方英雄艾希（编者注：寒冰射手）进入中毒状态。现在，给出提莫对艾希的攻击时间序列和提莫攻
击的中毒持续时间，你需要输出艾希的中毒状态总时长。你可以认为提莫在给定的时间点进行攻击，并立即使艾希处于中毒状态。

#### 用例
输入: \[1,4], 2

输出: 4

输入: \[1,2], 2

输出: 3
#### 解题思路
单词扫描就可以
#### 代码
```python
class Solution:
    def findPoisonedDuration(self, timeSeries: List[int], duration: int) -> int:
        ans = 0
        n = len(timeSeries)
        for i in range(n - 1):
            if timeSeries[i] + duration < timeSeries[i + 1]:
                ans += duration
            else:
                ans += timeSeries[i + 1] - timeSeries[i]
        ans += duration
        return ans

```

### 题目[497. 非重叠矩形中的随机点](https://leetcode-cn.com/problems/random-point-in-non-overlapping-rectangles/)
给定一个非重叠轴对齐矩形的列表 rects，写一个函数 pick 随机均匀地选取矩形覆盖的空间中的整数点。

提示：
1. 整数点是具有整数坐标的点。
2. 矩形周边上的点包含在矩形覆盖的空间中。
3. 第 i 个矩形 rects\[i] = \[x1，y1，x2，y2]，其中\[x1，y1] 是左下角的整数坐标，\[x2，y2] 是右上角的整数坐标。
4. 每个矩形的长度和宽度不超过 2000。
5. 1 <= rects.length <= 100
6. pick 以整数坐标数组\[p_x, p_y] 的形式返回一个点。
7. pick 最多被调用10000次。


#### 用例
```
输入: 
["Solution","pick","pick","pick"]
[[[[1,1,5,5]]],[],[],[]]
输出: 
[null,[4,1],[4,1],[3,3]]
```
#### 解题思路
按照面积为每个举行分配权重，同时为保证每个点能代表一个单位矩形，需要对边长增加1
#### 代码
```python
class Solution:

    def __init__(self, rects: List[List[int]]):
        self.rects = rects
        self.size = len(rects)
        self.weight = []
        s = 0
        for a, b, c, d in rects:
            area = (c - a + 1) * (d - b + 1)  # 每个只会选择左上角的点
            s += area
            self.weight.append(s)

    def pick(self) -> List[int]:
        index = bisect.bisect_left(self.weight, random.randint(1, self.weight[-1]))
        x1, y1, x2, y2 = self.rects[index]
        return [random.randint(x1, x2), random.randint(y1, y2)]
```

### 题目[498.对角线遍历](https://leetcode-cn.com/problems/diagonal-traverse/)
给定一个含有 M x N 个元素的矩阵（M 行，N 列），请以对角线遍历的顺序返回这个矩阵中的所有元素，对角线遍历如下图所示。

#### 用例
```
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]

输出:  [1,2,4,7,5,3,6,8,9]
```

#### 解题思路
1. 利用栈模拟运行
2. 利用指针

#### 代码
```python
from typing import List
from collections import deque


class Solution:
    def findDiagonalOrder(self, matrix: List[List[int]]) -> List[int]:
        stack = deque()
        n = len(matrix)
        if not n:
            return []
        ans = []
        stack.append(matrix[0])
        turn = True
        i = 1
        while stack:
            stacksceond = deque()
            while stack:
                l = stack.popleft()
                ans.append(l.pop(0))
                if len(l) != 0:
                    if turn:
                        stacksceond.append(l)
                    else:
                        stacksceond.appendleft(l)
            if i < n:
                stacksceond.append(matrix[i])
                i += 1
            stack = stacksceond
        return ans


class Solution:
#https://leetcode-cn.com/problems/diagonal-traverse/solution/dui-jiao-xian-bian-li-by-leetcode/
    def findDiagonalOrder(self, matrix: List[List[int]]) -> List[int]:

        # Check for an empty matrix
        if not matrix or not matrix[0]:
            return []

        # The dimensions of the matrix
        N, M = len(matrix), len(matrix[0])

        # Incides that will help us progress through
        # the matrix, one element at a time.
        row, column = 0, 0

        # As explained in the article, this is the variable
        # that helps us keep track of what direction we are
        # processing the current diaonal
        direction = 1

        # Final result array that will contain all the elements
        # of the matrix
        result = []

        # The uber while loop which will help us iterate over all
        # the elements in the array.
        while row < N and column < M:

            # First and foremost, add the current element to
            # the result matrix.
            result.append(matrix[row][column])

            # Move along in the current diagonal depending upon
            # the current direction.[i, j] -> [i - 1, j + 1] if
            # going up and [i, j] -> [i + 1][j - 1] if going down.
            new_row = row + (-1 if direction == 1 else 1)
            new_column = column + (1 if direction == 1 else -1)

            # Checking if the next element in the diagonal is within the
            # bounds of the matrix or not. If it's not within the bounds,
            # we have to find the next head.
            if new_row < 0 or new_row == N or new_column < 0 or new_column == M:

                # If the current diagonal was going in the upwards
                # direction.
                if direction:

                    # For an upwards going diagonal having [i, j] as its tail
                    # If [i, j + 1] is within bounds, then it becomes
                    # the next head. Otherwise, the element directly below
                    # i.e. the element [i + 1, j] becomes the next head
                    row += (column == M - 1)
                    column += (column < M - 1)
                else:

                    # For a downwards going diagonal having [i, j] as its tail
                    # if [i + 1, j] is within bounds, then it becomes
                    # the next head. Otherwise, the element directly below
                    # i.e. the element [i, j + 1] becomes the next head
                    column += (row == N - 1)
                    row += (row < N - 1)

                # Flip the direction
                direction = 1 - direction
            else:
                row = new_row
                column = new_column

        return result
```