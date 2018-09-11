---
title: 【LeetCode】709. to Lower Case
date: 2018-08-23 20:02:07
tags: ['leetcode', 'javascript', '字符串']
---

> 题目连接: https://leetcode.com/problems/to-lower-case/description/

### 题目描述

- 输入: 一个字符串
- 输出: 把字符串里的所有字母都转换成小写

### demo

- 输入: Hello
- 输出: hello

### 思路

判断如果是大写字母，则先转换为 ascii 码，然后计算出相应小写字母的 ascii 码再转换为字母

### 代码

```
/**
 * @param {string} str
 * @return {string}
 */
var toLowerCase = function(str) {
    var res = '';
    for(var i = 0; i < str.length; i++) {
        s = str[i];
        if (str[i] >= 'A' && str[i] <= 'Z') {
            s = String.fromCharCode(parseInt(str[i].charCodeAt() + 32, 10));
        }
        res = res + s;
    }
    
    delete i;
    return res;
};
```
