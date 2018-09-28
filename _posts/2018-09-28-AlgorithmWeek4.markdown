---
layout:     post
title:      "House Robber"
subtitle:   "Week 4 -- LeetCode -- Dynamic Programming"
date:       2018-09-28 20:00:00
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、题目

[**House Robber**](https://leetcode.com/problems/house-robber/description/)

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

> Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

**Example 1:**
```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**
```
Input: [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

### 二、分析&解题
数组下标代表房子序号，值代表该房能被偷出的金钱，不能偷相邻的房子的情况下，问能偷出的最大金钱数是多少。

因为是求和最大值，则有如下规律：
1. 假设从左往右开始偷，不能回头，当前偷的房子序号为n，则下一栋的房子序号应为n+2和n+3中的钱多者（若不存在，则相当于钱为0）
2. 必偷第0栋和第1栋间的钱多者

#### 思路一：
动态规划
1. 保存中间结果。用一个序列`maxAmountStartFromN`保存从当前下标开始往右偷，能偷到的最大金钱数，以减少重复运算。如`maxAmountStartFromN[0]`代表从第0栋开始偷，能偷到的最大金钱数。**若不做这一步则会超时**
2. 边界。从倒数第一、第二栋开始偷能偷到的最大金钱就是其本身的金钱数
3. 子问题。即上面所说的规律：`maxAmountStartFromN[n] = nums[n] + max(maxAmountStartFromN[n + 2], maxAmountStartFromN[n + 3])`

JS实现：

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var rob = function(nums) {
    var len = nums.length;
    var maxAmountStartFromN = new Array(len).fill(-1);
    maxAmountStartFromN[len - 1] = nums[len - 1];
    maxAmountStartFromN[len - 2] = nums[len - 2];
    
    //从第robbedIndex栋开始往后偷，能获得的最大金钱
    function getMaxFromNthStart(robbedIndex) {
        //边界
        if(robbedIndex >= nums.length - 2) {
            if(robbedIndex < nums.length) {
                return maxAmountStartFromN[robbedIndex];
            }
            return 0;
        }
        //偷紧邻的下一栋的下一栋能获得的最大金钱
        var nextNext = (maxAmountStartFromN[robbedIndex + 3] != -1 && maxAmountStartFromN[robbedIndex + 3] != undefined) ? maxAmountStartFromN[robbedIndex + 3] : getMaxFromNthStart(robbedIndex + 3);
        //偷紧邻的下一栋能获得的最大金钱
        var next = (maxAmountStartFromN[robbedIndex + 2] != -1 && maxAmountStartFromN[robbedIndex + 2] != undefined) ? maxAmountStartFromN[robbedIndex + 2] : getMaxFromNthStart(robbedIndex + 2);
        //保存中间结果，以减少重复运算
        maxAmountStartFromN[robbedIndex] = nums[robbedIndex] + Math.max(nextNext, next);
        return maxAmountStartFromN[robbedIndex];
    }
    
    return Math.max(getMaxFromNthStart(1), getMaxFromNthStart(0));
};
```

提交结果：

![AC](/img/in-post/Algorithm/HouseRobber/JSac.png)
