---
layout:     post
title:      "Perfect Squares"
subtitle:   "Week 13 -- LeetCode -- Dynamic Programming"
date:       2018-12-10 23:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、题目

[**Perfect Squares**](https://leetcode.com/problems/perfect-squares)

> Given a positive integer n, find the least number of perfect square numbers (for example, `1, 4, 9, 16, ...`) which sum to n.

**Example 1:**
```
Input: n = 12
Output: 3 
Explanation: 12 = 4 + 4 + 4.
```

**Example 2:**
```
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

**Note:**
1. The integer `n` is in the range [1, 1000] and `k` is in the range [0, 1000].

### 二、分析&解题

给定一个正整数`n`，将其分解成平方数之和的形式，求平方数的最少个数

#### 思路一：递归

用最常见的DP思路去分析，不难发现可以提炼出以下子问题：  
`对于给定n，同时指定其分解式必须包含一个数m，求这种情况下的最少平方数个数`  
这里为了方便起见，定义`m`：可取的最大平方数的平方根，初值为不大于`n`的最大平方数的平方根
则得出转移方程式：  
`p(n, m1) = min(1 + p(n - m1, m2), p(n, m1 - 1))`  
即求`n`的最小平方数，可以分解为两个子问题：  
1. 假如我用`m`去分解`n`，那么其最少平方数个数是多少
2. 假如我用`m以外`的平方数去分解`n`，那么其最少平方数个数是多少
然后取这两种情况中的小者，即为原问题的解  
这里关键点就在于如何确保`m`的取值覆盖了所有可用平方数，  
这里我的解决方法是将`m`初值定为不大于`n`的最大平方数的平方根，而后`m以外`则用递归`m-1`的方式来实现  

边界情况为`n==0或m < 1`（两者虽然同为边界，但处理却有所不同，具体看代码吧）

JS实现：
``` javascript
/**
 * @param {number} n
 * @return {number}
 */
var numSquares = function(n) {
    var perfect = new Array(n + 1).fill(0);
    perfect[1] = 1;
    var max = Math.floor(Math.sqrt(n));
    
    function dp(n, max) {
        if(n == 0) return 0;
        if(max < 1) return Infinity;
        
        if(!perfect[n]) {
            var nextN = n - Math.pow(max, 2);
            var nextMax = Math.floor(Math.sqrt(nextN));
            perfect[n] = Math.min(1 + dp(nextN, nextMax), dp(n, max - 1));
        }
        return perfect[n];
    }
    return dp(n, max);
};
```

提交结果：
![AC](/img/in-post/Algorithm/PerfectSquares/JSAC-1.png)

时间有点不理想，想了想还是`m`的取值上有点问题，重复情况有点多

#### 思路二：递推

本质和思路一是一样的，但这种实现却简单高效了许多。  
我们让一个序列来存储严格满足题目要求的解，即  
`p[n]`：`n`的最小平方数个数  
同时我们知道对一个给定的`n`，其分解式中能出现的平方数是有限的：
`1...m`，其中`m`为不大于`n`的最大平方数的平方根  
那么我们就能得出一条递推式：  
`p[n]=min(1 + p[n - 1*1], ..., 1 + p[n - m*m])`  
剩下初值的处理直接看代码吧  

代码如下：
```javascript
/**
 * @param {number} n
 * @return {number}
 */
var numSquares = function(n) {
    var perfect = new Array(n + 1).fill(Infinity);
    perfect[0] = 0;
    for (var i = 1; i <= n; i++) {
        for(var j = 1; j * j <= i; j++) {
            perfect[i] = Math.min(perfect[i], perfect[i - j * j] + 1);
        }
    }
    return perfect[n];
};
```

提交结果：
![AC](/img/in-post/Algorithm/PerfectSquares/JSAC-2.png)
