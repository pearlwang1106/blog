---
title: 【LeetCode】848. Shifting Letters
date: 2018-08-24 10:41:57
tags: ['leetcode', 'javascript', '字符串']
---

> 题目连接: https://leetcode.com/problems/shifting-letters/description/

### 题目描述

给定一个字符串 s 和一个整数数组 shifts。字符串中的所有字母都是 'a' 到 'z' 之间的小写字母。有如下规则：shift('a') = 'b'; shift('t') = 'u'; shift('z') = 'a'。现在根据给定的数组 shifts，如果 shifts[i] = x，表示把 s 字符串中的前 i+1 个字母 shift x 次。

### demo

- 输入: 'abc', [3, 5, 9]
- 输出: 'rpl'
- 解释: 
	- 'a' 转换 3 次是 'dbc'
	- 'db' 分别转换 5 次是 'igc'
	- 'igc' 分别转换 9 次是 'rpl'

### 思路

- 第一种，双重for循环，第一层是 shifts 数组（循环变量用 i 表示），第二层是字符串 s（循环变量用 就
表示：0 <= j <=i），每次将 s 的前 j 个字母转换 shifts[i] 次。
- 第二种，分析字符串 s 的第一个字母，第一次转换 shifts[0] 次，第二次再转换 shifts[1] 次，所以一共转换了 shifts[0] + ... + shifts[n - 1] 次；同理，s 的第二个字母一共转换了 shifts[0] + ... + shifts[n - 2] 次；... 所以首先利用 shifts 数组计算出 s 中每个字母应该转换的次数，这里需要注意由于是 'a' 到 'z' 循环，所以对 26 取余。两次遍历即可解决，时间复杂度是O(N)

### 代码

```
/**
 * @param {string} S
 * @param {number[]} shifts
 * @return {string}
 */
var shiftingLetters = function(S, shifts) {
    var r = '';
    var n = 0;
    
    // 计算出每位字符应该变换的次数
    for(var i = shifts.length - 2; i >= 0; i--) {
        shifts[i] = shifts[i] + shifts[i + 1];
    }
    
    for(var i = 0; i < shifts.length; i++) {
       n = parseInt(S[i].charCodeAt() + shifts[i] % 26, 10);
        if (n > 122) {
            n = 96 + (n - 122) % 26;
        }
        r += String.fromCharCode(n);
    }
    delete n;
    return r;
};
```

