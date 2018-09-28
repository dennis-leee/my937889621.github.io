---
layout:     post
title:      "Median of Two Sorted Arrays"
subtitle:   "Week 1 -- LeetCode -- Divede and Conquer"
date:       2018-09-09 18:30:00
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Divide and Conquer
---

### 一、题目

[**Median of Two Sorted Arrays**](https://leetcode.com/problems/median-of-two-sorted-arrays/description/)

> There are two sorted arrays nums1 and nums2 of size m and n respectively.
> Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
> You may assume nums1 and nums2 cannot be both empty.

**Example 1:**
```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**
```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

### 二、分析&解题

先看下题目，已知两个已排序的数组**不全为空**，求其中值，要求时间复杂度为O(log(m+n)).  
注意，这里题目说的是已排序，实际输入已默认两个都是升序。（可能我的英语理解有点问题

#### 思路一：
最朴素（懒）的解法，将两个数组合并后排序，再取中值即可，代码如下：

``` javascript
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
    var allNums = nums1.concat(nums2);
    allNums.sort(function(v1, v2) {
        return v1 - v2;
    });
    return allNums.length % 2 == 0 ? (allNums[allNums.length / 2 - 1] + allNums[allNums.length / 2]) / 2 : allNums[Math.floor(allNums.length / 2)];
};
```
说点题外话，JS的API看似方便，但一不注意还是会踩到坑XD.
这里要说的是Array对象的sort函数，MDN的介绍如下：
> The sort() method sorts the elements of an array in place and returns the array. The sort is not necessarily stable. **The default sort order is according to string Unicode code points.**

看最后一句话，不传参数的话，默认采用Unicode编码排序。什么是Unicode编码在这里就不多说了。总之，要实现纯数字的排序，则需要一个排序函数作为参数，上面代码中传入的匿名函数即为一个升序的排序函数。

提交结果如下：
![easy](/img/in-post/Algorithm/MedianOfTwoSortedArrays/result-1.png)
果然耗时不怎么理想呢OAO

#### 思路二：

整体思路：分别遍历两个数组，重新搭建一个有序数列，这样从理论上来讲只用遍历一半的总长度即可取到中位数。
~~PS：整体思路看似简单，实际实现上还是有挺多小坑点的~~
废话不多说，先上代码：

``` javascript
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
    var iter1 = iter2 = 0;
    var bigger, smaller; //当前有序队列中的最大值和次大值
    var halfLength = (nums1.length + nums2.length) / 2;
    var isOdd = (halfLength % 1 != 0);
    halfLength = Math.floor(halfLength);
    if(halfLength == 0) {
        bigger = nums1[iter1] === undefined ? nums2[iter2] : nums1[iter1];
        return bigger;
    }
    for(var i = 0; i <= halfLength; i++) {
        smaller = bigger;
        if(nums1[iter1] === undefined) {
            bigger = nums2[iter2 + (halfLength - i)];
            if(smaller === undefined) {  //若smaller无值，则初始化smaller
                if(iter2 + halfLength - i > 0) {
                    smaller = nums2[iter2 + (halfLength - i) - 1];
                }
                else {
                    smaller = nums1[iter1 - 1];
                }
            }
            else if((iter2 + halfLength - i > 0) && (nums2[iter2 + (halfLength - i) - 1] > smaller)) {
                smaller = nums2[iter2 + (halfLength - i) - 1];
            }
            break;
        }
        if(nums2[iter2] === undefined) {
            bigger = nums1[iter1 + (halfLength - i)];
            if(smaller === undefined) {
                if(iter1 + halfLength - i > 0) {
                    smaller = nums1[iter1 + (halfLength - i) - 1];
                }
                else {
                    smaller = nums2[iter2 - 1];
                }
            }
            else if((iter1 + halfLength - i > 0) && (nums1[iter1 + (halfLength - i) - 1] > smaller)) {
                smaller = nums1[iter1 + (halfLength - i) - 1];
            }
            break;
        }
        if(nums1[iter1] < nums2[iter2]) {
            bigger = nums1[iter1++];
        }
        else {
            bigger = nums2[iter2++];
        }
    }
    return isOdd ? bigger : (bigger + smaller) / 2;
};
```

解析：
用两个iter分别做两个数组的下标，用两个变量bigger和smaller分别存储当前重构有序数列的最大值和次大值，计算总长度的一半，若为整数则最后返回bigger和smaller的均值，否则返回bigger。  
特殊情况：两数组总长度为1，特殊处理，直接判断后返回。  
剩下的循环处理即可：
1. 判断两数组下标是否越界（即某一数组已遍历完成），越界则计算当前重构数列的个数与中位的差值，直接跳到中位取值即可。
2. 无越界则正常取两数组间的最小值，作为当前重构数列的最大值bigger。

有一点需要特别注意的是，**每次循环判断取的是小的那个值作为bigger（而不是smaller），然后再下一次循环开头将smaller更新为上一循环的bigger，再重新求下一个bigger。**  
为什么要这么做？因为我重构的是**升序**数列，而每次比较只能保证**小的那个值**是重构的已排序数列中的最大值，而不能保证**大的那个值**是重构的已排序数列中的最大值。

可能有点绕，结合之前我对bigger和smaller的定义就好理解了。

提交结果如下：

![normal](/img/in-post/Algorithm/MedianOfTwoSortedArrays/result-2.png)
还行吧，快了不少XD。
