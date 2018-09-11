---
title: 【LeetCode】7. reverse Integer
date: 2018-08-23 08:58:32
tags: ['leetcode', 'javascript']
---

> 题目连接: https://leetcode.com/problems/reverse-integer/description/

### 题目描述

- 输入: 一个整数 x
- 输出: 将 x 的每一位数字逆序输出
- 注意: 整数区间  [−2的31次方,  2的31次方 − 1], 负数的负号要一直在首位，逆序后的数字也要在整数区间内

### demo

- case1
    - 输入: 123
    - 输出: 321
- case2
    - 输入: -123 
    - 输出: -321
- case3
    - 输入: 1200
    - 输出: 21

### 思路

先转换成字符串再反转，方便处理

### 代码

```
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function(x) {
    if (x === 0 || x < -1 * Math.pow(2, 31) || x > Math.pow(2, 31) - 1) {
        return 0;
    }
    var y = x.toString();
    var s = '';
    var flag = 0;
    for (var i = y.length - 1; i >= 0; i--) {
        if (!flag && y[i] === '0') {
            continue;
        }
        flag = 1;
        s += y[i];
        if (i === 0 && y[0] === '-') {
            s = '-' + s;
        }
    }
    delete y;
    delete i;
    delete flag;
    s = parseInt(s);
    if (s < -1 * Math.pow(2, 31) || s  > Math.pow(2, 31) - 1) {
        return 0;
    }
    return parseInt(s);
};
```