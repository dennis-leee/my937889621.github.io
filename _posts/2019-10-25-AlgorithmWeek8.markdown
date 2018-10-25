---
layout:     post
title:      "Edit Distance"
subtitle:   "Week 8 -- LeetCode -- Dynamic Programming"
date:       2018-10-25 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、 [**Edit Distance**](https://leetcode.com/problems/edit-distance/description/)

##### 1. Description

> Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

> You have the following 3 operations permitted on a word:

1. Insert a character
2. Delete a character
3. Replace a character

**Example 1:**
```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**
```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

##### 2. Solution

编辑距离  
将一个字符串变为另一个字符串最少需要多少次增/删/替换操作  

首先考虑一种对齐方式：在一个字符串的上方书写另一个字符串，如对于BLACK和LOCK，其中两种对齐方式如下  
```
B　L　A　C　K　　　　　-　B　L　A　C　K
-　L　O　C　K　　　　　L　-　O　-　C　K
   代价：2                代价：4
```
其中`-`代表一个`空隙`，对齐时可将它随意插入到每个字符串中  
`代价`是指上下字符串对应子明不相同的列数
这样求解编辑距离就变成了求两字符串的各种对齐所可能具有的最小代价  

将两字符串分别记为数组`x[1...m]`、`y[1...n]`，其前缀`x[1...i] (i <= m)`、`y[1...j] (j <= n)`  
`E(m, n)`为两串间的编辑距离，则`E(i, j)`为两子串间的编辑距离  

用动态规划求解`E(i, j)`，先看最后一列的对齐情况，其有三种可能 ：
```
x[i]        -         x[i]
 -         y[j]       y[j]
```
对于情况一，需要对x进行一次`删除`操作，其代价为1，
剩下子问题`x[1...i-1]`和`y[1...j]`如何最佳对齐，即`E(i, j) = 1 + E(i - 1, j)`  

对于情况二，需要对x进行一次`插入`操作，其代价为1，
剩下子问题`x[1...i]`和`y[1...j-1]`如何最佳对齐，即`E(i, j) = 1 + E(i, j - 1)`  

对于情况三：
若`x[i] == y[j]`，则其代价为0，
剩下子问题`x[1...i-1]`和`y[1...j-1]`如何最佳对齐，即`E(i, j) = E(i, j - 1)`  
若`x[i] != y[j]`，则需要对x进行一次`替换`操作，其代价为1，
剩下子问题`x[1...i-1]`和`y[1...j-1]`如何最佳对齐，即`E(i, j) = 1 + E(i, j - 1)`   

我们需要在这三种情况中选一个代价最小的最为结果，即
`E(i, j) = Min{1 + E(i - 1, j), 1 + E(i, j - 1), diff + E(i, j - 1)}`  
其中`diff = (x[i] == y[j]) ? 0 : 1`  


这样求解`E(m, n)`就可以被分解为求解若干个`E(i, j)`子问题
易知`E(i, 0) = i`、`E(0, j) = j` 这样递归就有了边界，  
将`E`看做一个二维表（数组），我们需要做的就是从`E(0, 0)`开始填满`E(m, n)`

JS实现如下：

``` javascript
/**
 * @param {string} word1
 * @param {string} word2
 * @return {number}
 */
var minDistance = function(word1, word2) {
    var E = new Array();
    var len1 = word1.length;
    var len2 = word2.length;
    for(var i = 0; i <= len1; i++) {
        E.push([i]);
    }
    for(var j = 1; j <= len2; j++) {
        E[0].push(j);
    }
    for(var i = 1; i <= len1; i++) {
        for(var j = 1; j <= len2; j++) {
            var diff = word1[i - 1] == word2[j - 1] ? 0 : 1;
            E[i][j] = Math.min(E[i - 1][j] + 1, E[i][j - 1] + 1, E[i - 1][j - 1] + diff);
        }
    }
    return E[len1][len2];
};
```

提交结果：

![AC](/img/in-post/Algorithm/EditDistance/1-JSAC.png)



### 二、 [**Delete Operation for Two Strings**](https://leetcode.com/problems/delete-operation-for-two-strings/description/)

##### 1. Description

> Given two words word1 and word2, find the minimum number of steps required to make word1 and word2 the same, where in each step you can delete one character in either string.

**Example 1:**
```
Input: "sea", "eat"
Output: 2
Explanation: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".
```
**Note:**
1. The length of given words won't exceed 500.
2. Characters in given words can only be lower-case letters.

##### 2. Solution

问题一的变种，将两个字符串变成一个相同串至少需要多少次删除操作

回想对齐一列的三种情况：
```
x[i]        -         x[i]
 -         y[j]       y[j]
```

对于情况一、二，我们只需要一次`删除`操作即可，代价仍为1，子问题也不变
对于情况三，两者相同代价为0，不同时则需要两次`删除`操作，代价为2，子问题不变

因此，我们唯一需要改动的就是`diff = (x[i] == y[j]) ? 0 : 2`

代码就不贴了，提交结果如下：

![AC](/img/in-post/Algorithm/EditDistance/2-JSAC.png)

PS:今年的网易游戏秋招就有一题本质和这题一样的：
将一个字符串变成另一个字符串，至少需要多少次增/删操作
说法上有点不同，转换成代码实际上是一样的，这里就不赘述了

### 三、 [**Minimum ASCII Delete Sum for Two Strings**](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/description/)

##### 1. Description

> Given two strings s1, s2, find the lowest ASCII sum of deleted characters to make two strings equal.

**Example 1:**
```
Input: s1 = "sea", s2 = "eat"
Output: 231
Explanation: Deleting "s" from "sea" adds the ASCII value of "s" (115) to the sum.
Deleting "t" from "eat" adds 116 to the sum.
At the end, both strings are equal, and 115 + 116 = 231 is the minimum sum possible to achieve this.
```

**Example 2:**
```
Input: s1 = "delete", s2 = "leet"
Output: 403
Explanation: Deleting "dee" from "delete" to turn the string into "let",
adds 100[d]+101[e]+101[e] to the sum.  Deleting "e" from "leet" adds 101[e] to the sum.
At the end, both strings are equal to "let", and the answer is 100+101+101+101 = 403.
If instead we turned both strings into "lee" or "eet", we would get answers of 433 or 417, which are higher.
```

**Note:**
- `0 < s1.length, s2.length <= 1000`.
- All elements of each string will have an ASCII value in `[97, 122]`.

##### 2. Solution

问题二的变种  
只能进行删除操作，将两个字符串变成一个相同串  
求所删除的字符的ASCII码之和的最小值  

乍看之下，好像不太一样。仔细思考，我们之前将`一次操作的代价记为1`
即不同操作的区别可以看做只是理解的不同，实质上都是等价的  
更一般的，不妨将`代价`称作`权重`，问题就归结为`求权重（代价）和最小`  
之前的问题都是`对某一字符进行各操作的权重均为1`，故而这里我们`将每个字符的ASCII码作为对其进行操作（删除）的权重`不就行了吗？  

简单修改一下之前的代码：
``` javascript
/**
 * @param {string} s1
 * @param {string} s2
 * @return {number}
 */
var minimumDeleteSum = function(s1, s2) {
    var WE = new Array();
    var len1 = s1.length;
    var len2 = s2.length;
    WE.push([0]);
    for(var i = 0; i < len1; i++) {
        WE.push([WE[i][0] + s1[i].charCodeAt()]);
    }
    for(var j = 0; j < len2; j++) {
        WE[0].push(WE[0][j] + s2[j].charCodeAt());
    }
    for(var i = 1; i <= len1; i++) {
        for(var j = 1; j <= len2; j++) {
            var diff = s1[i - 1] == s2[j - 1] ? 0 : s1[i - 1].charCodeAt() + s2[j - 1].charCodeAt();
            WE[i][j] = Math.min(WE[i - 1][j] + s1[i - 1].charCodeAt(), WE[i][j - 1] + s2[j - 1].charCodeAt(), WE[i - 1][j - 1] + diff);
        }
    }
    return WE[len1][len2];
};
```

提交结果：

![AC](/img/in-post/Algorithm/EditDistance/3-JSAC-1.png)

时间不是很理想
优化点：
1. 空间换时间。将字符串数组转为对应的ASCII码数组，减少实时计算次数
2. 回想对齐一列的三种情况：
```
x[i]        -         x[i]
 -         y[j]       y[j]
```
对第一、二种情况的处理不变，对第三种情况：
当两者相同，代价0，
两者不同，将其分解为前面两种情况

修改后代码：
``` javascript
/**
 * @param {string} s1
 * @param {string} s2
 * @return {number}
 */
var minimumDeleteSum = function(s1, s2) {
    var WE = new Array();
    var len1 = s1.length;
    var len2 = s2.length;
    var w1 = new Array(len1);
    var w2 = new Array(len2);
    WE.push([0]);
    for(var i = 0; i < len1; i++) {
        w1[i] = s1[i].charCodeAt();
        WE.push([WE[i][0] + w1[i]]);
    }
    for(var j = 0; j < len2; j++) {
        w2[j] = s2[j].charCodeAt();
        WE[0].push(WE[0][j] + w2[j]);
    }
    for(var i = 1; i <= len1; i++) {
        for(var j = 1; j <= len2; j++) {
            if(w1[i - 1] == w2[j - 1]) {
                WE[i][j] = WE[i - 1][j - 1]
            }
            else {
                WE[i][j] = Math.min(WE[i - 1][j] + w1[i - 1], WE[i][j - 1] + w2[j - 1]);
            }
        }
    }
    return WE[len1][len2];
};
```

提交结果：
![AC](/img/in-post/Algorithm/EditDistance/3-JSAC-2.png)
