---
layout:     post
title:      "Walking Robot Simulation"
subtitle:   "Week 7 -- LeetCode -- Greedy"
date:       2018-10-21 23:00:00
author:     Dennis
header-img: ""
tags:
    - JavaScript
    - Algorithm
    - Greedy
---

### 一、题目

[**Walking Robot Simulation**](https://leetcode.com/problems/walking-robot-simulation/description/)

> A robot on an infinite grid starts at point (0, 0) and faces north.  The robot can receive one of three possible types of commands:

- `2`: turn left 90 degrees
- `1`: turn right 90 degrees
- `1 <= x <= 9`: move forward `x` units
> Some of the grid squares are obstacles. 

> The i-th obstacle is at grid point `(obstacles[i][0], obstacles[i][1])`

> If the robot would try to move onto them, the robot stays on the previous grid square instead (but still continues following the rest of the route.)

> Return the **square** of the maximum Euclidean distance that the robot will be from the origin.

**Example 1:**
```
Input: commands = [4,-1,3], obstacles = []
Output: 25
Explanation: robot will go to (3, 4)
```

**Example 2:**
```
Input: commands = [4,-1,4,-2,4], obstacles = [[2,4]]
Output: 65
Explanation: robot will be stuck at (1, 4) before turning left and going to (1, 8)
```

### 二、分析&解题

模仿机器人的行动，求**执行过程中的与原点(0, 0)最大欧几里得距离的平方**
机器人有三种指令：左转、右转、前进N步
地图上会有障碍物，预定路线中有障碍物时，停在障碍物的前一个位置。

存储三个状态信息：
- `direction`: 机器人朝向
- `x`： 机器人X坐标
- `y`： 机器人Y坐标

遍历指令数组，对状态信息进行相应变动，每执行完一次移动指令后计算距离，与上一位置比较，取大者即可。
关键在于如何优化执行速度

#### 思路一：

每执行一次移动指令，就遍历一遍路障数组，看是否有路障
这样时间复杂度粗略可以看做`O（N+n*K）`，N、K分别为指令、路障数组长度，n为移动指令数

JS实现：

``` javascript
/**
 * @param {number[]} commands
 * @param {number[][]} obstacles
 * @return {number}
 */
var robotSim = function(commands, obstacles) {
    var robot = {
        direction: 0,    // 0-north, 1-east, 2-south, 3-west
        location: [0, 0] // (x, y)
    };
    
    function move(step) {
        var cantMoveIndex = robot.direction % 2;
        var moveIndex = (cantMoveIndex + 3) % 2;
        var gotStuck = false;
        var minDistance = 0;
        for(var i = 0; i < obstacles.length; i++) {
            if(robot.location[cantMoveIndex] == obstacles[i][cantMoveIndex]) {
                var distance = obstacles[i][moveIndex] - robot.location[moveIndex];
                if(distance > 0 && robot.direction < 2 && step >= distance) {      // north or east
                    if(!gotStuck) {
                        gotStuck = true;
                        minDistance = distance;
                    }
                    if(distance < minDistance) minDistance = distance;
                }
                else if(distance < 0 && robot.direction > 1 && step >= -distance) { // south or west
                    distance = -distance;
                    if(!gotStuck) {
                        gotStuck = true;
                        minDistance = distance;
                    }
                    if(distance < minDistance) minDistance = distance;
                }
            }
        }
        if(robot.direction > 1) {
            robot.location[moveIndex] -= gotStuck ? minDistance - 1 : step;
        }
        else {
            robot.location[moveIndex] += gotStuck ? minDistance - 1 : step;
        }
    }
    
    var max = 0;
    //do commands
    for(var i = 0; i < commands.length; i++) {
        if(commands[i] == -2) robot.direction = (robot.direction + 3) % 4;      // turn left
        else if(commands[i] == -1) robot.direction = (robot.direction + 5) % 4; // turn right
        else {
            move(commands[i]);
            var t = Math.pow(robot.location[0], 2) + Math.pow(robot.location[1], 2);
            if(t > max) max = t;
        }
    }
    return max;
};
```

提交结果：

![AC](/img/in-post/Algorithm/WalkingRobotSimulation/JSAC-1.png)

#### 思路二：

采用二维Map数据结构来存储路障坐标信息，第一位存储X信息，第二维存储Y信息。
利用Map.get()来判断是否有指定路障，因为该函数是非线性查找，~~目测是树~~，因此理论上会比线性快。


JS实现：

``` javascript
/**
 * @param {number[]} commands
 * @param {number[][]} obstacles
 * @return {number}
 */
var robotSim = function(commands, obstacles) {
    var direction = 0;  // 0-north, 1-east, 2-south, 3-west
    var x = 0;
    var y = 0;
    var moveX = [0, 1, 0, -1];
    var moveY = [1, 0, -1, 0];
    var obMap = new Map();
    obstacles.forEach(function(ob) {
        if(!obMap.get(ob[0])) obMap.set(ob[0], new Map());
        obMap.get(ob[0]).set(ob[1], true);
    });
    
    var max = 0;
    //do commands
    var cLen = commands.length;
    for(var i = 0; i < cLen; i++) {
        if(commands[i] == -2) direction = (direction + 3) % 4;      // turn left
        else if(commands[i] == -1) direction = (direction + 1) % 4; // turn right
        else {
            var sLen = commands[i];
            var nextX = x;
            var nextY = y;
            for(var j = 0; j < sLen; j++) {
                nextX += moveX[direction];
                nextY += moveY[direction];
                if(obMap.get(nextX)) if(obMap.get(nextX).get(nextY)) break;
                x = nextX;
                y = nextY;
            }
            max = Math.max(max, x * x + y * y);
        }
    }
    return max;
};
```

提交结果：

![AC](/img/in-post/Algorithm/WalkingRobotSimulation/JSAC-2.png)
