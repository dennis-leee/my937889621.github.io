---
layout:     post
title:      "Continuous Subarray Sum"
subtitle:   "Week 9 -- LeetCode -- Math"
date:       2018-11-02 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Math
---

### 一、题目

[**Continuous Subarray Sum**](https://leetcode.com/problems/walking-robot-simulation/description/)

> Given a list of **non-negative** numbers and a target **integer** k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to the multiple of **k**, that is, sums up to n*k where n is also an **integer**.

**Example 1:**
```
Input: [23, 2, 4, 6, 7],  k=6
Output: True
Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
```

**Example 2:**
```
Input: [23, 2, 6, 4, 7],  k=6
Output: True
Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
```

**Note:**
> 1. The length of the array won't exceed 10,000.
> 2. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.

### 二、分析&解题

给定一个非负整数数组，问能否找到一个子数组，使其满足：
1. 元素在原数组中的位置连续
2. 元素和 = n * k（n ∈ 整数，k为给定整数）
3. 数组长度 >= 2

#### 思路一：

暴力解法：  
遍历所有可能的情况，若存在Sum，使得Sum % k == 0，则返回True.  
时间复杂度：O（n^2）  

轻优化：  
1. 保存部分Sum，通过这部分Sum之间的减法运算求得其他Sum，以减少计算次数
2. 保存(Sum % k)，而不是直接保存Sum，减少计算难度  
   （已知`a = A % k`, `b = B % k`, 若`(a + b) % k == 0`, 则`(A + B) % k == 0`）
特殊情况：
1. 数组长度 < 2，直接返回false
2. k = 0，问题变为能否找到连续的两个0，特殊处理

JS实现：
``` javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {boolean}
 */
var checkSubarraySum = function(nums, k) {
    var len = nums.length;
    if(len < 2) return false;
    if(k == 0) {
        var zero1 = nums.indexOf(0)
        for(; zero1 != -1; zero1 = nums.indexOf(0, zero1)) {
            if(nums[++zero1] == 0) return true;
            else ++zero1;
        }
        return false;
    }
    //remainderOfSumAfterNth[n]：nums[n]及其之后的值之和除以k的余数
    var remainderOfSumAfterNth = new Array(len);
    remainderOfSumAfterNth[len - 1] = nums[len - 1] % k;
    for(var i = len - 2; i >= 0; i--) {
        remainderOfSumAfterNth[i] = (nums[i] + remainderOfSumAfterNth[i + 1]) % k;
    }
    
    for(var i = 0; i < len - 1; i++) {
        var remainder = remainderOfSumAfterNth[i];
        if(remainder == 0) return true;
        else {
            for(var j = i + 2; j < len; j++) {
                var tRe = (remainder - remainderOfSumAfterNth[j] + k) % k;
                if(tRe == 0) return true;
            }
        }
    }
    
    return false;
};
```

提交结果：
![AC](/img/in-post/Algorithm/ContinuousSubarraySum/JSAC-1.png)

#### 思路二：

数学解法：  
已知`a = A % k`, `b = B % k`, 若`a == b`, 则`(A - B) % k == 0`  
粗略上看和思路一的优化原理差不多，但实现上可以有很大区别。对变量的说明如下：  
`Sum`: 从第1个元素到第`i`个元素的值之和 除以 k 的`余数`  
`lastSum`：顾名思义，上一个`Sum`，即从第1个元素到第`i - 1`个元素的值之和 除以 k 的`余数`  
`remainderOfSumBeforeNth[n]`：nums[n]及其之前的值之和 除以 k 的`余数`  

让`Sum`和`remainderOfSumBeforeNth`的`i`相差为`2`（确保满足`数组长度 >= 2`的条件）  
这样，当`Sum==0`或`remainderOfSumBeforeNth`中存在一个值与当前`Sum`值相同时，即为`True`

emmm，直接看代码可能比较清晰XD  

JS实现：
``` javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {boolean}
 */
var checkSubarraySum = function(nums, k) {
    var len = nums.length;
    if(len < 2) return false;
    if(k == 0) {
        var zero1 = nums.indexOf(0)
        for(; zero1 != -1; zero1 = nums.indexOf(0, zero1)) {
            if(nums[++zero1] == 0) return true;
            else ++zero1;
        }
        return false;
    }
    var lastSum = nums[0] % k;
    var sum = (lastSum + nums[1]) % k;
    if(!sum) return true;
    
    var remainderOfSumBeforeNth = new Array();
    for(var i = 2; i < len; i++) {
        remainderOfSumBeforeNth.push(lastSum);
        lastSum = sum;
        sum = (sum + nums[i]) % k;
        if(sum == 0 || remainderOfSumBeforeNth.indexOf(sum) != -1) return true;
    }
    
    return false;
};
```

提交结果：
![AC](/img/in-post/Algorithm/ContinuousSubarraySum/JSAC-2.png)

PS：leetcode给这题打了`Dynamic Programming`的标签，但以上两种解法貌似都不是`Dynamic Programming`  
暂时也想不出用`Dynamic Programming`要怎么解，想到再填坑吧
