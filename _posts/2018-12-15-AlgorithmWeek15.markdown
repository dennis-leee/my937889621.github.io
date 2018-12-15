---
layout:     post
title:      "Triangle"
subtitle:   "Week 15 -- LeetCode -- Dynamic Programming"
date:       2018-12-15 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、题目

[**Triangle**](https://leetcode.com/problems/triangle/)

> Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

> For example, given the following triangle
```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```
> The minimum path sum from top to bottom is `11` (i.e., **2** + **3** + **5** + **1** = 11).

**Note:**
> Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.

### 二、分析&解题

给定一个二维数组`triangle`，该数组可以展开成如上例所示的三角形  
规定每一层的一个位置（下标：n）可以到达下一层的两个相邻的位置（n、n+1）  
而每个位置有相应的权重（值）  
现求一条能从三角形顶端到达底部的路线，要求该路线权重最小  

#### 思路一：自顶向下

简单的DP问题，不难发现子问题为：  
`可选的两个路径中，哪个权重最小`  
因此，我们定义一个一样的数据结构来存储最小路径的权重值即可：  
`minimum[row][col]：以第row行，第col列为起点，到达底部的最小路径的权重值`  
于是该问题的解就变成求解`minimum[0][0]`  
不难得出递推式：  
`minimum[row][col] = triangle[row][col] + min(minimum[row+1][col], minimum[row+1][col+1])`  
最后一行即为边界，值和三角形中最后一行的值相同

~~PS：好久没遇到这么经典的DP问题了！是不是很简单？！~~

JS实现：
``` javascript
/**
 * @param {number[][]} triangle
 * @return {number}
 */
var minimumTotal = function(triangle) {
    let height = triangle.length;
    let minimum = new Array(height);
    for(let i = 0; i < height - 1; i++) {
        minimum[i] = new Array();
    }
    minimum[height - 1] = triangle[height - 1];
    
    function minPath(row, col) {
        if(minimum[row][col] === undefined) {
            let nextRow = row + 1;
            let nextCol = col + 1;
            minimum[row][col] = triangle[row][col] + Math.min(minPath(nextRow, col), minPath(nextRow, nextCol));
        }
        return minimum[row][col];
    }
    
    return minPath(0, 0);
};
```

提交结果：
![AC](/img/in-post/Algorithm/Triangle/JSAC-1.png)

#### 思路二：自底向上

注意到Note里面的Bonus是空间复杂度为O(n)，n为三角形的行数，  
而思路一的解法空间复杂度应为O(n*(n+1)/2)，即O(n^2)  

不难发现问题的核心就是`两两对比，取最小`  
那么我们是不是可以直接从最后一行开始处理？  
即另：`minimum = triangle[len - 1]`，然后  

1. 用`minimum[i] = min(minimum[i], minimum[i + 1])`来扫描队列，选出相邻的两个数中的小者，将放在前面  
2. 加上上一层对应位置的权重（舍弃最后一个位置）  

这样是不是就达到了和思路一中`minimum[row][col]`一样的效果？  
于是按照这个处理过程，一层一层往上处理，最后即得问题的解

JS实现：
``` javascript
/**
 * @param {number[][]} triangle
 * @return {number}
 */
var minimumTotal = function(triangle) {
    let len = triangle.length;
    let minimum = triangle[len - 1];
    for(let row = len - 1; row > 0; row--) {
        let lastRow = row - 1;
        for(let col = 0; col < row; col++) {
            minimum[col] = triangle[lastRow][col] + Math.min(minimum[col], minimum[col + 1]);
        }
    }
    return minimum[0];
};
```

提交结果：
![AC](/img/in-post/Algorithm/Triangle/JSAC-2.png)
