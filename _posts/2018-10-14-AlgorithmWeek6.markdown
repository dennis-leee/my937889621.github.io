---
layout:     post
title:      "House Robber III"
subtitle:   "Week 6 -- LeetCode -- Depth-first Search"
date:       2018-10-14 20:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Depth-first Search
---

### 一、题目

[**House Robber III**](https://leetcode.com/problems/house-robber-iii/description/)

> The thief has found himself a new place for his thievery again. There is only one entrance to this area, called the "root." Besides the root, each house has one and only one parent house. After a tour, the smart thief realized that "all houses in this place forms a binary tree". It will automatically contact the police if two directly-linked houses were broken into on the same night.

> Determine the maximum amount of money the thief can rob tonight without alerting the police.

**Example 1:**
```
Input: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

Output: 7 
Explanation: Maximum amount of money the thief can rob = 3 + 3 + 1 = 7.
```

**Example 2:**
```
Input: [3,4,5,1,3,null,1]

     3
    / \
   4   5
  / \   \ 
 1   3   1

Output: 9
Explanation: Maximum amount of money the thief can rob = 4 + 5 = 9.
```

### 二、分析&解题
[**House Robber**](https://leetcode.com/problems/house-robber/description/)的升级版，房子序列变成了树状结构，输入为树节点的形式，输出在不取直接相连的两个节点的情况下，能取到的最多钱数。

#### 思路一：

考虑几种情况：
1. 传入节点为空节点时，返回0
2. 传入节点无子节点时，直接返回当前节点的值
3. 传入节点有子节点时，返回Max(当前节点值，子节点值之和)
4. 传入节点有子节点，且子节点也有子节点时（3层树），返回Max(1、3层值之和， 2层值之和， 2层左值和2层右节点的子节点值之和， 2层右值和2层左节点的子节点值之和)

定义空节点等效于值为0，则1、2、3其实均为4的特殊情况，而4中的后3种情况也可归为一种情况：将左右子节点分别作为根，传入函数中，计算函数输出之和
这样就涵盖了所有情况

JS实现：

``` javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var rob = function(root) {
    if(root == null) return 0;
    var left = root.left === null ? 0 : rob(root.left.left) + rob(root.left.right);
    var right = root.right === null ? 0 : rob(root.right.left) + rob(root.right.right);
    return Math.max(left + right + root.val, rob(root.left) + rob(root.right));
};
```

提交结果：

![AC](/img/in-post/Algorithm/HouseRobber/3-JSAC.png)

耗时较多，优化考虑保存中间结果，减少运算。
