---
title: leetcode-day19
date: 2020-06-05 15:28:49
tags:
- leetcode
- 算法
- java
categories:
- 程序人生
- 算法
---

![图片](http://api.mtyqx.cn/api/random.php?145)
<!-- more -->


### 题目[面试题29.顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

#### 用例
```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

#### 解题思路
定好遍历时的边界，再模拟遍历过程进行遍历

#### 代码

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        int row = matrix.length;
        if (row==0)return new int[0];
        int col = matrix[0].length;
        int[] result = new int[row * col];
        int k = 0;
        int idx = 0;
        while (k < (Math.min(col, row) + 1) / 2) {
            int i = k, j = k;
            while (j < col - k) {
                result[idx++] = matrix[i][j];
                j++;
            }
            j--;
            while (++i < row - k) {
                result[idx++] = matrix[i][j];
            }
            i--;
            while (--j >= k && i > k) {
                result[idx++] = matrix[i][j];
            }
            j++;
            while (--i > k && j <col - k -1) {
                result[idx++] = matrix[i][j];
            }
            k++;
        }

        return result;
    }
}
class Solution2 {
    public int[] spiralOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return new int[0];
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[] order = new int[rows * columns];
        int index = 0;
        int left = 0, right = columns - 1, top = 0, bottom = rows - 1;
        while (left <= right && top <= bottom) {
            for (int column = left; column <= right; column++) {
                order[index++] = matrix[top][column];
            }
            for (int row = top + 1; row <= bottom; row++) {
                order[index++] = matrix[row][right];
            }
            if (left < right && top < bottom) {
                for (int column = right - 1; column > left; column--) {
                    order[index++] = matrix[bottom][column];
                }
                for (int row = bottom; row > top; row--) {
                    order[index++] = matrix[row][left];
                }
            }
            left++;
            right--;
            top++;
            bottom--;
        }
        return order;
    }
}
```

### 题目[539.最小时间差](https://leetcode-cn.com/problems/minimum-time-difference/)
给定一个 24 小时制（小时:分钟）的时间列表，找出列表中任意两个时间的最小时间差并以分钟数表示。
#### 用例
```
输入: ["23:59","00:00"]
输出: 1
```
#### 解题思路
排序
#### 代码
```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        int[] times = new int[timePoints.size()];
        int i = 0;
        for (String timePoint : timePoints) {
            int time = Integer.parseInt(timePoint.substring(0,2))*60+Integer.parseInt(timePoint.substring(3,5));
            times[i] = time;
            i++;
        }
        Arrays.sort(times);
        int result = 1440 - times[times.length-1]+times[0];
        for (int j = 1; j < times.length; j++) {
            result = Math.min(result,times[j]-times[j-1]);
        }
        return result;
    }
}
```
### 题目[540.有序数组中的单一元素](https://leetcode-cn.com/problems/single-element-in-a-sorted-array/)
给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。
#### 用例
```
输入: [1,1,2,3,3,4,4,8,8]
输出: 2
```

```
输入: [3,3,7,7,10,11,11]
输出: 10
```
#### 解题思路
1. 二分法求解
    1. 将其分为两部分，并比较mid与前后两数是否相同
    2. 如果mid与前面的mid-1相同，判断mid-left的奇偶性，奇数那么单个元素出现在mid之后，left=mid+1，偶数，那么单个元素出现在mid之前，right=mid
    3. 如果mid与后面的mid+1相同，判断mid-left的奇偶性，奇数那么单个元素出现在mid之前，right=mid-1，偶数那么出现在mid之后，left=mid
    4. 如果都不相同，那就找到这个数了
2. 只检查索引为偶数的元素
    1. 奇数长度的数组首尾元素索引都为偶数，因此我们可以将 lo 和 hi 设置为数组首尾。
    2. 我们需要确保 mid 是偶数，如果为奇数，则将其减 1。
    3. 然后，我们检查 mid 的元素是否与其后面的索引相同。
    4. 如果相同，则我们知道 mid 不是单个元素。且单个元素在 mid 之后。则我们将 lo 设置为 mid + 2。
    5. 如果不是，则我们知道单个元素位于 mid，或者在 mid 之前。我们将 hi 设置为 mid。
    6. 一旦 lo == hi，则当前搜索空间为 1 个元素，那么该元素为单个元素，我们将返回它。

#### 代码

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int left = 0, right = nums.length-1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid]==nums[mid-1]){
                if ((mid-left) % 2 == 0 ){
                    right = mid;
                }else left = mid+1;
            }else if (nums[mid]==nums[mid+1]) {
                if ((mid - left) % 2 == 0) {
                    right = mid;
                } else left = mid + 1;
            }else return nums[mid];
        }
        return nums[left];
    }
}

class Solution2 {
    public int singleNonDuplicate(int[] nums) {
        int lo = 0;
        int hi = nums.length - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (mid % 2 == 1) mid--;
            if (nums[mid] == nums[mid + 1]) {
                lo = mid + 2;
            } else {
                hi = mid;
            }
        }
        return nums[lo];
    }
}
```