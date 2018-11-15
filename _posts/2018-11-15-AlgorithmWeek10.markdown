---
layout:     post
title:      "String to Integer (atoi)"
subtitle:   "补Week 10 -- LeetCode -- Math"
date:       2018-11-15 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - String
    - Math
---

### 一、题目

[**String to Integer (atoi)**](https://leetcode.com/problems/string-to-integer-atoi/description/)

> Implement `atoi` which converts a string to an integer.

> The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

> The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

> If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

> If no valid conversion could be performed, a zero value is returned.

**Note:**

- Only the space character `' '` is considered as whitespace character.
- Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. If the numerical value is out of the range of representable values, INT_MAX (231 − 1) or INT_MIN (−231) is returned.

**Example 1:**
```
Input: "42"
Output: 42
```

**Example 2:**
```
Input: "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign.
             Then take as many numerical digits as possible, which gets 42.
```

**Example 3:**
```
Input: "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.
```

**Example 4:**
```
Input: "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical 
             digit or a +/- sign. Therefore no valid conversion could be performed.
```

**Example 5:**
```
Input: "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer.
             Thefore INT_MIN (−231) is returned.
```

### 二、分析&解题

实现`atoi`函数（字符串转换成整数）  
1. 从左至右扫描，遇到的数字之前的`' '`可以无视
2. 识别符号`+`、`-`，默认为正数（两个及以上符号时，立即终止）
3. 遇到除了以上三种符号以外的字符，立即终止，返回当前扫描到的整数
4. 没识别到整数时，返回0
5. 转换的范围为32位有符号整数，即`[−2^31,  2^31 − 1]`，超出范围返回边界值

补充两个比较容易忽略的样例：
```
Input: "++1"
Output: 0
```
```
Input: "-001"
Output: -1
```

#### 思路一：

思路比较简单，按照分析的情况一个一个处理就行了：
1. 去除左边的`' '`
2. 发现第一个`'+'`、`'-'`，则记`foundSymbol = true`，并记录`Symbol`（默认`'+'`）
3. 对得到的数字进行累加：`num = num * 10 + currentNum`
4. 最后判断正负号，以及是否超出边界即可

JS实现：
``` javascript
/**
 * @param {string} str
 * @return {number}
 */
var myAtoi = function(str) {
    var MAXINT32 = Math.pow(2, 31) - 1;
    var MININT32 = -Math.pow(2, 31);
    str = str.trimLeft();
    var symbol = '+';
    var foundSymbol = false;
    var counting = false;
    var num = 0;
    for (var i = 0, len = str.length; i < len; i++) {
        if (str[i] === '+' || str[i] === '-') {
            if (foundSymbol || counting) break;
            foundSymbol = true;
            symbol = str[i];
            continue;
        }
        if (str[i] >= '0' && str[i] <= '9') {
            num = num * 10 + (str[i] - '0');
            counting = true;
            continue;
        }
        break;
    }
    if (symbol === '-') num = -num;
    if (num > MAXINT32) num = MAXINT32;
    if (num < MININT32) num = MININT32;
    return num;
};
```

提交结果：
![AC](/img/in-post/Algorithm/StringToInteger/JSAC-1.png)

不是很理想，优化一下：  
考虑`'0'`的特殊性，可以单独提出来处理，减少运算  
在两个if间插入如下代码：  

``` javascript
if (str[i] === '0' && !counting) {
    counting = true;
    continue;
}
```

提交结果：
![AC](/img/in-post/Algorithm/StringToInteger/JSAC-2.png)
