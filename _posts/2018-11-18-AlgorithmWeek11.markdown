---
layout:     post
title:      "Maximum Product Subarray"
subtitle:   "Week 11 -- LeetCode -- Dynamic Programming"
date:       2018-11-18 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
    - Array
---

### 一、题目

[**Maximum Product Subarray**](https://leetcode.com/problems/maximum-product-subarray/)

> Given an integer array `nums`, find the contiguous subarray within an array (containing at least one number) which has the largest product.

**Example 1:**
```
Input: [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```

**Example 2:**
```
Input: [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
```

### 二、分析&解题

给定一个不为空的整数数组，定义`乘积`为其一个连续子数组的所有元素的乘积，求最大`乘积`

#### 思路一：（Time Limit Exceeded）

运用DP常规解法分析，得出以下规律：
1. 数组`长度为1`时，直接返回当前元素
2. 数组`长度为2`时，比较`第一个元素`、`第二个元素`、以及`它们的乘积`，返回最大值。即子问题边界
3. 数组`长度>2`时，可将其拆解为两个数组，分别求这两个数组的最大`乘积`，以及当前数组的`乘积`，比较这三者，返回最大值。即子问题分解
4. 保存特定数组的最大`乘积`，重复使用减少运算

如何拆解数组呢？比较暴力（超时主要原因？）：  
给定一个数组`[1, 2, 3, ..., n]`，定义一个拆解下标，初值为1,  
即首次拆解为`[1]`和`[2, 3, ..., n]`，随后递增：`[1, 2]`和`[3, ..., n]`,   
直至`[1, 2, 3, ..., n - 1]`和`[n]`,  
用一个循环实现即可

JS实现：
``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxProduct = function(nums) {
    var maxWithinSAndE = new Map();
    function getMaxProduct(start, end) {
        var len = end - start;
        if (len == 1) return nums[start];
        var current = 's' + start + 'e' + end;
        if (!maxWithinSAndE.get(current)) {
            var max = eval(nums.slice(start, end).join('*'));
            for (var i = 1; i < len; i++) {
                var division = start + i;
                max = Math.max(getMaxProduct(start, division), getMaxProduct(division, end), max);
            }
            maxWithinSAndE.set(current, max);
        }
        return maxWithinSAndE.get(current);
    }
    return getMaxProduct(0, nums.length);
};
```

提交结果：
![TLE](/img/in-post/Algorithm/MaximumProductSubarray/JSTLE.png)

差两个样例没过，自己在控制台试下了这个超时输入，结果是正确的  
~~所以这解法是没什么问题，就是太暴力...~~

#### 思路二：

参考大佬的解法，思路清奇简单，遍历一遍数组即可... 

首先，**不考虑负数的情况**，那么问题是不是就很简单了？  
假设现在遍历下标为`i`  
定义`max`：遍历累乘至`i - 1`时的最大值  
更新`max = Math.max(nums[i], nums[i] * max)`  
（从`nums[i]`开始累乘，或继续之前的累乘，确保满足`连续子数组`这个条件）  
同时用另一个变量`ans`记录`max`的最大值  

转换为代码：
```javascript
function (nums) {
    var ans = nums[0];
    var max = nums[0];
    for (var i = 1, len = nums.length; i < len; i++) {
        max = Math.max(nums[i], nums[i] * max);
        ans = Math.max(max, ans);
    }
    return ans;
}
```

是不是很简单？！  
现在再考虑上**有负数**的情况，  
类似的，定义一个`min`：遍历累乘至`i - 1`时的最小值  
更新`max = Math.max(nums[i], nums[i] * max, nums[i] * min)`  
`min = Math.min(nums[i], nums[i] * max, nums[i] * min)`  
注意到，判断里面多了`nums[i] * min`，这样就处理了当min为负数时，遇到下一个数为负数，负负得正这种情况  

转换为代码并优化一下：
```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxProduct = function(nums) {
    var ans = nums[0];
    var max = nums[0];
    var min = nums[0];
    for (var i = 1, len = nums.length; i < len; i++) {
        var nextMax = nums[i] * max;
        var nextMin = nums[i] * min;
        min = Math.min(nums[i], nextMax, nextMin);
        max = Math.max(nums[i], nextMax, nextMin);
        ans = Math.max(max, ans);
    }
    return ans;
};
```

提交结果：
![AC](/img/in-post/Algorithm/MaximumProductSubarray/JSAC.png)
