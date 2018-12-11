---
layout:     post
title:      "Race Car"
subtitle:   "Week 14 -- LeetCode -- Dynamic Programming"
date:       2018-12-11 13:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Dynamic Programming
---

### 一、题目

[**Race Car**](https://leetcode.com/problems/race-car/)

> Your car starts at position 0 and speed +1 on an infinite number line.  (Your car can go into negative positions.)

> Your car drives automatically according to a sequence of instructions A (accelerate) and R (reverse).

> When you get an instruction "A", your car does the following: `position += speed`, `speed *= 2`.

> When you get an instruction "R", your car does the following: if your speed is positive then `speed = -1` , otherwise `speed = 1`.  (Your position stays the same.)

> For example, after commands "AAR", your car goes to positions 0->1->3->3, and your speed goes to 1->2->4->-1.

> Now for some target position, say the **length** of the shortest sequence of instructions to get there.

**Example 1:**
```
Input: 
target = 3
Output: 2
Explanation: 
The shortest instruction sequence is "AA".
Your position goes from 0->1->3.
```

**Example 2:**
```
Example 2:
Input: 
target = 6
Output: 5
Explanation: 
The shortest instruction sequence is "AAARA".
Your position goes from 0->1->3->7->7->6.
```

**Note:**
- `1 <= target <= 10000`

### 二、分析&解题

简单翻译一下：  
给你一辆车，初位置为`p = 0`，初速为`s = +1`  
当其接收到指令`A`：`p += s`， `s *= 2`  
当其接收到指令`R`：`s`为正数则令`s = -1`，否则`s = +1`（**p保持不变**）  
可输入一串指令，如`'AAR'`，让车移动  
先给定目标位置`target`，求到达`target`的最短指令的长度  

#### 思路一：

定义`dp[n]`：到达目标`n`所需要的最短指令的长度
不难看出车的位置`p`跟A的个数`n`满足关系：`p = 2^n - 1`(等比数列求和)  
于是就有两种情况：
1. 车刚好能行驶到`target`处，此时`n`即为最短指令长度
2. 车只能到底一个接近`target`的点，即`2^(n - 1) - 1 < t < 2^n - 1`而后转头逼近`target`
   - `2^n - 1`：此时需调头，即进行一次'R'指令，而后行驶`2^n - 1 - target`个距离，而因为转头后速度初始化为1，故其需要的最短指令数可用`dp[n]`来求，即`dp[target] = b + 1 + dp[2^n - 1 - target]`，如下图所示：  
     ![s1](/img/in-post/Algorithm/RaceCar/Graphic-1.png)
   - `2^(n - 1) - 1`：为什么还需要考虑这种情况呢？回想此时`2^(n - 1) - 1 < p < 2^n - 1`  
     当`t`距离前者很近，后者很远时，距离短的需要的时间短是很自然的想法，这里只是把时间换成`dp[n]`  
     此时需要两次`R`来重置速度，虽然比后置多了1次`R`，但省下可能不止1个`A`  
     再深入考虑一下如果该距离（`p`与`t`的距离，此时`p = 2^(n - 1) - 1`）不满足`p = 2^n - 1`（这里n∈非负整数），那么可能需要再转头一次来逼近，这显然不好，在不必要的情况下，`R`的次数显然是越少越好的，如何解决？  
     可以在两次`R`的中间插入`A`，让与先向后移动一段距离，使得`p`与`t`的距离满足`p = 2^n - 1`，而后在调头前进（这里向后行驶的`A`指令上限为`n - 2`个， 为何？可以假设取`n-1`甚至`n`代入分析，可以发现其是不必要的）  
     可以结合下图理解这种情况：  
     ![s2](/img/in-post/Algorithm/RaceCar/Graphic-2.png)

JS实现：
``` javascript
/**
 * @param {number} target
 * @return {number}
 */
var racecar = function(target) {
    // The length of shortest sequence
    let dp = new Array(target + 1).fill(0);
    function race(target) {
        if (dp[target]) return dp[target];
        // The number of 'A's that just exceed or reach the goal
        let n = Math.floor(Math.log(target) / Math.log(2)) + 1;
        if ((1 << n) - 1 == target) {
            // just reach the goal
            dp[target] = n;
        } else {
            // exceed the goal and turn back
            dp[target] = race((1 << n) - 1 - target) + n + 1;
            // another way: just almost reach the goal
            // then take a brake or even go turn back to find a suitable distance, then drive toward the goal again
            for (let m = 0; m < n - 1; m++) {
                dp[target] = Math.min(dp[target], race(target - (1 << (n - 1)) + (1 << m)) + n + m + 1);
                // Here, the explanation of the second parameter. (draw a picture is easier to understand)
                // It can be divided into two parts
                // Firstly, race(target - (1 << (n - 1)) + (1 << m))
                /**
                 * target: the goal we need to reach
                 * (1 << (n - 1)) - 1: the point before the goal
                 * (1 << m) - 1: the distance we need to turn back
                 * combine above, it's (target - ((1 << (n - 1)) - 1)) + ((1 << m) - 1)
                 * so it's the distance between target and the second turning point point
                 */
                // secondly, n + m + 1
                // In fact, it should be (n - 1)'A' + 1'R' + m'A' + 1'R'
            }
        }
        return dp[target];
    }
    return race(target)
};
```

提交结果：
![AC](/img/in-post/Algorithm/RaceCar/JSAC-1.png)
