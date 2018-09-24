---
layout:     post
title:      "Reverse Pairs"
subtitle:   "LeetCode算法题"
date:       2018-09-16 12:00:00
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - C++
    - Algorithm
    - Divede and Conquer
---

### 一、题目

**Reverse Pairs**

> Given an array `nums`, we call `(i, j)` an **important reverse pair** if `i < j`   
> and `nums[i] > 2*nums[j]`.  
> You need to return the number of important reverse pairs in the given array.

**Example 1:**
```
Input: [1,3,2,3,1]
Output: 2
```

**Example 2:**
```
Input: [2,4,3,5,1]
Output: 3
```

### 二、分析&解题

#### 思路一：
最朴素（懒）的解法，取一个值，跟他后面所有的值比较，满足关系则计数+1，代码实现就一个两重循环：

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var reversePairs = function(nums) {
    var nums2 = nums.map(function(val) {
        return val * 2;
    });
    var len = nums.length;
    var count = 0;
    for(var i = 0; i < len - 1; i++) {
        for(j = i + 1; j < len; j++) {
            if(nums[i] > nums2[j]) {
                count++;
            }
        }
    }
    return count;
};
```
不出所料，超时了XD

![TimeLimitExceeded](/img/in-post/Algorithm/ReversePairs/TimeLimitExceeded.png)

#### 思路二：

仔细思考题目给出的两个条件：
1. `i < j`
2. `nums[i] > 2 * nums[j]`  
先看第二个，不难想到，若数组是**有序**的，则不用再遍历整个数组（Hint: 下标差值）。  
再看第一个，要比较下标，则不能直接排完序再去筛选，需要在排序过程中进行下标比较。  
再翻翻[各个经典排序算法的性能比较](https://www.jianshu.com/p/7d037c332a9d)，经过一番剧烈的思考，最终选择了较容易理解又刚好符合我们分析出的条件的归并排序。

总体思路：利用归并先拆后并的特性，分别计算两个子串内部的count，再加上两个子串间的count，在递归合并的过程中同时递归count计数。

初步实现代码：

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var reversePairs = function(nums) {
    return mergeSortAndCount({nums:nums});  //伪引用传递
};

function merge(left, right) {
    var re = [];
    while(left.length > 0 && right.length > 0) {
        if(left[0] < right[0]) {
            re.push(left.shift());
        }
        else {
            re.push(right.shift());
        }
    }
    return re.concat(left).concat(right);
}

function mergeSortAndCount(arr) {
    if(arr.nums.length <= 1) return 0;
    var start = 0;
    var end = arr.nums.length - 1;
    var mid = Math.ceil((start + end) / 2);
    var left = {nums: arr.nums.slice(0, mid)};
    var right = {nums: arr.nums.slice(mid)};
    //分别计算两子串各自的计数
    var count = mergeSortAndCount(left) + mergeSortAndCount(right);
    //再加上两子串间的计数
    for(var i = 0; i < mid; i++) {
        for(var j = 0; j <= end - mid; j++) {
            //因为是有序的，所以可以直接定位到临界值，拿总长-临界值下标即可获得计数
            if(left.nums[i] <= right.nums[j] * 2) {
                break;
            }
        }
        count += j;
    }
    arr.nums = merge(left.nums, right.nums);
    return count;
}
};
```

解析：  
考虑当算法将数组拆至最简，如`left = [3]`、`right = [9]`时，  
先分别看两个子串，满足题目两个条件的计数**均为0**，  
再看两串间，由于`left的下标`永远小于`right的下标`，故第一个条件已满足，  
剩下的就是比较值的大小，若满足条件则计数 + 1，否则不加。

则`当前串的计数 = 两子串内的计数 + 两子串间的计数`，  
即`currentCount = (leftCount + rightCount) + combineCount //在本例中为(0 + 0) + 0`,  
将该计数返回给上层，这样递归往上传递，最终即可得出整个数组的计数。

自己测试了几个样例，都没问题，提交！

![TimeLimitExceeded](/img/in-post/Algorithm/ReversePairs/jsTimeLimitExceeded.png)

φ( °-°)/ ... 没关系，咱们换用C++实现

![TimeLimitExceeded](/img/in-post/Algorithm/ReversePairs/cppTimeLimitExceeded.png)

ヽ(｀Д´)ﾉ︵ ┻━┻ ┻━┻ 

搞了一晚上，实在没辙了，翻了翻别人的解题思路，用归并的基本大同小异，为啥我的就超时呢QAQ，最后找到了一个[容易理解又相似的实现](https://leetcode.com/problems/reverse-pairs/discuss/97295/C++-Clean-and-Short-MergeSort-Based)，  
仔细对比实现才发现了一个不同------**计算两子串间的计数的方法**，仔细看了下自己的代码，最后才恍然大悟，以下是修改前后对比：

**修改前**
``` javascript
    //再加上两子串间的计数
    for(var i = 0; i < mid; i++) {
        for(var j = 0; j <= end - mid; j++) {
            //因为是有序的，所以可以直接定位到临界值，拿总长-临界值下标即可获得计数
            if(left.nums[i] <= right.nums[j] * 2) {
                break;
            }
        }
        count += j;
    }
```

**修改后**
``` javascript
    //再加上两子串间的计数
    for(var i = 0, j = 0; i < mid; i++) {
        //因为是有序的，所以可以直接定位到临界值，拿总长-临界值下标即可获得计数
        while(j < end - mid && left.nums[i] > right.nums[j] * 2)
            j++
        count += j;
    }
```

差别就在于每次执行外层循环时，修改前的内层循环j重置了，实际上是不用重置的。  
至于原因稍微思考一下就懂了。

提交，终于过了〒▽〒 ~~菜鸡落泪.jpg~~
~~耗时优化待续~~

![jsAC](/img/in-post/Algorithm/ReversePairs/jsAC.png)

最后附上C++代码：


``` cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        return mergeSortAndCount(nums, 0, nums.size());
    }

    void merge(vector<int>& nums, int start, int mid, int end) {
        vector<int> temp;
        int i = start;
        int j = mid;
        while (i < mid && j < end) {
            if (nums[i] < nums[j])
                temp.push_back(nums[i++]);
            else
                temp.push_back(nums[j++]);
        }
        if (j < end)
            temp.insert(temp.end(), nums.begin() + j, nums.begin() + end);
        else if (i < mid)
            temp.insert(temp.end(), nums.begin() + i, nums.begin() + mid);
        int k = 0;
        while (start < end)
            nums[start++] = temp[k++];
    }

    int mergeSortAndCount(vector<int>& nums, int start, int end) {
        if (end - start <= 1) return 0;
        int mid = (start + end) / 2;
        int count = mergeSortAndCount(nums, start, mid) + mergeSortAndCount(nums, mid, end);
        for (int i = start, j = mid; i < mid; i++) {
            while (j < end && nums[i] > (long long)nums[j] * 2)
                j++;
            count += j - mid;
        }
        merge(nums, start, mid, end);
        return count;
    }
};
```
