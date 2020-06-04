---
title: leetcode-day18
date: 2020-06-04 10:10:06
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

![图片](http://api.mtyqx.cn/api/random.php?88)
<!-- more -->


### 题目[238.除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)
给你一个长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。

说明：
1. 提示：题目数据保证数组之中任意元素的全部前缀元素和后缀（甚至是整个数组）的乘积都在 32 位整数范围内。
2. 说明: 请不要使用除法，且在 O(n) 时间复杂度内完成此题。

#### 用例
```
输入: [1,2,3,4]
输出: [24,12,8,6]
```

#### 解题思路
1. 最好不要用除法，应为数组中的数字可能为0，会导致除0的错误
2. 对一个数字，求它的左连乘积以及右连乘积，就可以得到其结果

#### 代码
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] result = new int[nums.length];
        result[0] = 1;
        for (int i = 1; i < nums.length; i++) {
            result[i] = result[i - 1] * nums[i-1];
        }
        int R = 1;
        for (int i = nums.length - 1; i >= 0; i--) {
            result[i] = R * result[i];
            R *= nums[i];
        }
        return result;
    }
}
```


### 题目[535.TinyURL 的加密与解密](https://leetcode-cn.com/problems/encode-and-decode-tinyurl/)
TinyURL是一种URL简化服务， 比如：当你输入一个URL https://leetcode.com/problems/design-tinyurl 时，它将返回一个简化的URL http://tinyurl.com/4e9iAk.

要求：设计一个 TinyURL 的加密 encode 和解密 decode 的方法。你的加密和解密算法如何设计和运作是没有限制的，你只
需要保证一个URL可以被加密成一个TinyURL，并且这个TinyURL可以用解密方法恢复成原本的URL

#### 解题思路
1. 利用哈希表存储url映射

#### 代码
```java
class Codec {
    Map<Integer, String> map = new HashMap<>();
    int i = 0;

    public String encode(String longUrl) {
        map.put(i, longUrl);
        return "http://tinyurl.com/" + i++;
    }

    public String decode(String shortUrl) {
        return map.get(Integer.parseInt(shortUrl.replace("http://tinyurl.com/", "")));
    }
}

class Codec2 {

    String chars = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    HashMap<String, String> map = new HashMap<>();
    int count = 1;

    public String getString() {
        int c = count;
        StringBuilder sb = new StringBuilder();
        while (c > 0) {
            c--;
            sb.append(chars.charAt(c % 62));
            c /= 62;
        }
        return sb.toString();
    }

    public String encode(String longUrl) {
        String key = getString();
        map.put(key, longUrl);
        ++count;
        return "http://tinyurl.com/" + key;
    }

    public String decode(String shortUrl) {
        return map.get(shortUrl.replace("http://tinyurl.com/", ""));
    }
}


class Codec3 {
    Map<Integer, String> map = new HashMap<>();

    public String encode(String longUrl) {
        map.put(longUrl.hashCode(), longUrl);
        return "http://tinyurl.com/" + longUrl.hashCode();
    }

    public String decode(String shortUrl) {
        return map.get(Integer.parseInt(shortUrl.replace("http://tinyurl.com/", "")));
    }
}

class Codec4 {
    Map<Integer, String> map = new HashMap<>();
    Random r = new Random();
    int key = r.nextInt(Integer.MAX_VALUE);

    public String encode(String longUrl) {
        while (map.containsKey(key)) {
            key = r.nextInt(Integer.MAX_VALUE);
        }
        map.put(key, longUrl);
        return "http://tinyurl.com/" + key;
    }

    public String decode(String shortUrl) {
        return map.get(Integer.parseInt(shortUrl.replace("http://tinyurl.com/", "")));
    }
}

class Codec5 {
    String alphabet = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    HashMap<String, String> map = new HashMap<>();
    Random rand = new Random();
    String key = getRand();

    public String getRand() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 6; i++) {
            sb.append(alphabet.charAt(rand.nextInt(62)));
        }
        return sb.toString();
    }

    public String encode(String longUrl) {
        while (map.containsKey(key)) {
            key = getRand();
        }
        map.put(key, longUrl);
        return "http://tinyurl.com/" + key;
    }

    public String decode(String shortUrl) {
        return map.get(shortUrl.replace("http://tinyurl.com/", ""));
    }
}
```


### 题目[537.复数乘法](https://leetcode-cn.com/problems/complex-number-multiplication/)
给定两个表示复数的字符串。

返回表示它们乘积的字符串。注意，根据定义 i**2 = -1 。

#### 用例
输入: "1+1i", "1+1i"
输出: "0+2i"

输入: "1+-1i", "1+-1i"
输出: "0+-2i"

#### 代码
```java
class Solution {
    public String complexNumberMultiply(String a, String b) {
        String[] first = a.split("\\+|i");
        String[] second = b.split("\\+|i");
        int a_real = Integer.parseInt(first[0]);
        int a_img = Integer.parseInt(first[1]);
        int b_real = Integer.parseInt(second[0]);
        int b_img = Integer.parseInt(second[1]);
        return (a_real*b_real-a_img*b_img)+"+"+(a_real*b_img+b_real*a_img)+"i";
    }
}
```