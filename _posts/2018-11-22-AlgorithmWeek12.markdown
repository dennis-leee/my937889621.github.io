---
layout:     post
title:      "K Inverse Pairs Array"
subtitle:   "Week 12 -- LeetCode -- Dynamic Programming"
date:       2018-11-22 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、题目

[**K Inverse Pairs Array**](https://leetcode.com/problems/k-inverse-pairs-array/)

> Given two integers `n` and `k`, find how many different arrays consist of numbers from 1 to n such that there are exactly `k` inverse pairs.

> We define an inverse pair as following: For `i`<sub>th</sub> and `j`<sub>th</sub> element in the array, if `i < j` and `a[i] > a[j]` then it's an inverse pair; Otherwise, it's not.

> Since the answer may be very large, the answer should be modulo 10<sup>9</sup> + 7.

**Example 1:**
```
Input: n = 3, k = 0
Output: 1
Explanation: 
Only the array [1,2,3] which consists of numbers from 1 to 3 has exactly 0 inverse pair.
```

**Example 2:**
```
Input: n = 3, k = 1
Output: 2
Explanation: 
The array [1,3,2] and [2,1,3] have exactly 1 inverse pair.
```

**Note:**
1. The integer `n` is in the range [1, 1000] and `k` is in the range [0, 1000].

### 二、分析&解题

定义`逆对`：在一个数组`arr`中，第i个元素和第j个元素满足`i < j`且`arr[i] > arr[j]`，则称其为一个`逆对`  
现给定`n`、`k`，求解包含`1...n`的所有数组中，恰好有`k`个逆对的数组的数量。  

先列举一下，找规律：  
`[1, 2]`：仅移动`2`，可提供`0~1`个逆对  
加个3，相当于把`3`插入原来数组中的三个位置中的一个：
```
[ 1, 2 ]    或    [ 2, 1 ]
 ↑  ↑ ↑            ↑  ↑ ↑
```
先仅考虑`3`能提供的逆对，易知为`0~2`个，
那么同时移动`2`和`3`呢？其实就相当于`2`和`3`同时发力，即可提供`0+0~1+2 → 0~3`个逆对
这样就似乎就得出了n和k之间的一个关系：  

`1..n`的k逆对可以通过`1...n-1`的l逆对（l <= k）加上`n`单独提供的逆对得到  

如上例，我们要求`n=3，k=1`时的组合数，  
可以取`n=2，k=0`和`n=2，k=1`的情况分别在特定位置插入`3`，  
使得其提供的逆对数分别为`1`和`0`即可，故`n=3，k=1`有`0+1`和`1+0`两种组合数  

列个表印证一下：  

|k \ n| 1 | 2 | 3 | 4 |
| --- | -:| -:| -:| -:|
|  0  | 1 | 1 | 1 | 1 |
|  1  | 0 | 1 | 2 | 3 |
|  2  | 0 | 0 | 2 | 5 |
|  3  | 0 | 0 | 1 | 6 |
|  4  | 0 | 0 | 0 | 5 |
|  5  | 0 | 0 | 0 | 3 |
|  6  | 0 | 0 | 0 | 1 |

用`(n, k)`表示表中的对应项，也即问题的解。

易知k有约束：`k <= n * (n - 1) / 2`，在此范围外的k所对应的值均为0  
同时结合之前的推理和表，可得一递推公式：  
`(n, k) = (n - 1, k) + (n - 1, k - 1) + ... + (n - 1, k - n >= 0 ? k - n : 0)`  
不难看出，可以简化如下：  
`(n, k) = (n - 1, k) + (n, k - 1) - [k - n >= 0 ? (n - 1, k - n) : 0]`  

#### 思路一：

用<div title='感觉算不上，就一简单递归'>DP（？）</div>，递归到边界再填充至(n, k).

JS实现：
``` javascript
/**
 * @param {number} n
 * @param {number} k
 * @return {number}
 */
var kInversePairs = function(n, k) {
    var numOfPair = new Array();
    for(var i = 0; i <= n; i++) {
        var t = new Array(k + 1).fill(0);
        t[0] = 1;
        numOfPair.push(t);
    }
    var m = Math.pow(10, 9) + 7;
    
    function dp (n, k) {
        if (k <= n * (n - 1) / 2 && !numOfPair[n][k]) {
            numOfPair[n][k] = dp(n, k - 1) + dp(n - 1, k);
            if (k - n >= 0) {
                numOfPair[n][k] -= numOfPair[n - 1][k - n];
            }
            numOfPair[n][k] = (numOfPair[n][k] + m) % m;
        }
        return numOfPair[n][k];
    }
    return dp(n, k);
};
```

需要注意的是，**这里必须mod一个大数！**，因为到后面数字很大，可能会产生冲突  
参考![算法题目中，遇到结果是大数时，为什么喜欢 MOD 10^x+7 ?](https://www.zhihu.com/question/26127900)

提交结果：
![AC](/img/in-post/Algorithm/KInversePairsArray/JSAC-1.png)

这个解法，只填充了`k <= n * (n - 1) / 2`情况下的表项  
运算次数应该是较少的，但每次都要计算判断`k <= n * (n - 1) / 2`和该项是否已填充，  
以决定是否需要填充该项，因此导致耗时更久了

#### 思路二：

正向递推，按`n = 2， k = 0`、`n = 2， k = 1`、...、`n = n，k = k`的顺序填充表项
这种实现可不用判断`k <= n * (n - 1) / 2`和该项是否已填充  
`k`在范围外时，按`0`处理，然后按顺序填充下去就完了

代码如下：
```javascript
/**
 * @param {number} n
 * @param {number} k
 * @return {number}
 */
var kInversePairs = function(n, k) {
    var lastNumOfKPair = new Array(k + 1).fill(0);
    lastNumOfKPair[0] = 1;
    var m = Math.pow(10, 9) + 7;
    
    for (var i = 2; i <= n; i++) {
        var currentNumOfKPair = new Array(k + 1).fill(0);
        var t = 0;
        for (var j = 0; j <= k; j++) {
            t += lastNumOfKPair[j];
            if (j - i >= 0) t -= lastNumOfKPair[j - i];
            currentNumOfKPair[j] = (t + m) % m;
        }
        lastNumOfKPair = currentNumOfKPair;
    }
    return lastNumOfKPair[k];
};
```

提交结果：
![AC](/img/in-post/Algorithm/KInversePairsArray/JSAC-2.png)
