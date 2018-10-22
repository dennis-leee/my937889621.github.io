---
layout:     post
title:      "K-Similar Strings"
subtitle:   "Week 3 -- LeetCode -- Graph"
date:       2018-09-23 12:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - C++
    - Algorithm
    - Breadth-first Search
    - Graph
---

### 一、题目

[**K-Similar Strings**](https://leetcode.com/problems/k-similar-strings/description/)

> Strings `A` and `B` are `K`-similar (for some non-negative integer `K`) if we can swap the positions of two letters in `A` exactly `K` times so that the resulting string equals `B`.

> Given two anagrams `A` and `B`, return the smallest `K` for which `A` and `B` are `K`-similar.

**Example 1:**
```
Input: A = "ab", B = "ba"
Output: 1
```

**Example 2:**
```
Input: A = "abc", B = "bca"
Output: 2
```

**Example 3:**
```
Input: A = "abac", B = "baca"
Output: 2
```

**Example 4:**
```
Input: A = "aabc", B = "abca"
Output: 2
```

**Note:**

1. `1 <= A.length == B.length <= 20`
2. `A` and `B` contain only lowercase letters from the set `{'a', 'b', 'c', 'd', 'e', 'f'}`

### 二、分析&解题
给定字符串A，B，问由A变成B需要的最小交换次数k。A、B长度相等，且不存在A无法变成B的情况。

#### 思路一：
用BFS，记k为当前的交换次数：
1. 遍历一遍`交换一次`后的所有结果，若不存在`等于B`的结果，则交换下一次，存在则返回当前的k
2. 重复`1`至找到满足条件的k为止

注意：该方法非常耗时，故需尽可能找规律进行优化。

JS实现：

``` javascript
/**
 * @param {string} A
 * @param {string} B
 * @return {number}
 */
var kSimilarity = function(A, B) {
    var queueToBeTraversed = [];
    queueToBeTraversed.push({s:A, k:0});
    while(queueToBeTraversed.length > 0) {
        var current = queueToBeTraversed.shift();
        if(current.s == B) return current.k;
        //可以确保k-1以前的值都是相同的，故从k开始找不同
        for(var i = current.k; i < B.length - 1; i++) {
            if(current.s[i] != B[i]) break;
        }
        //填充下一轮(k+1)的所有可能字符串
        for(var j = i + 1; j < B.length; j++) {
            //找到所需字符后，拼接成与当前字符交换后的字符串，填充进遍历队列
            if(current.s[j] == B[i]) {
                var s = B.slice(0, i + 1) + current.s.slice(i + 1, j) + current.s[i] + current.s.slice(j + 1);
                queueToBeTraversed.push({s:s, k: current.k + 1});
            }
        }
    }
    return null;
};
```

超时

![TimeLimitExceeded](/img/in-post/Algorithm/K-SimilarStrings/JSTL.png)

换用C++实现：

``` cpp
struct KSObj {
    string s;
    int k;
    KSObj(string _s, int _k): s(_s), k(_k) {}
};

class Solution {
public:
    int kSimilarity(string A, string B) {
        list<KSObj> listToBeTraversed;
        KSObj t = {A, 0};
        listToBeTraversed.push_back(t);
        int len = B.length();
        while(listToBeTraversed.size() > 0) {
            KSObj current = listToBeTraversed.front();
            listToBeTraversed.pop_front();
            if(current.s == B) return current.k;
            //填充下一轮(k+1)的所有可能字符串
            int i = current.k;
            for(; i < len - 1; i++) {
                if(current.s[i] != B[i]) break;
            }  
            int j = i + 1;
            for(; j < len; j++) {
                if(current.s[j] == B[i]) {
                    //为了不改变原值，通过拼接实现交换
                    string s = B.substr(0, i + 1) + current.s.substr(i + 1, j - i - 1) + current.s[i] + current.s.substr(j + 1, len - j - 1);
                    KSObj t = {s, current.k + 1};
                    listToBeTraversed.push_back(t);
                }
            }
        }
        return NULL;
    }
};
```

勉强通过...

![AC](/img/in-post/Algorithm/K-SimilarStrings/CPPAC.png)

#### ~~思路二：~~

~~动态规划（待填充，学习中）~~
