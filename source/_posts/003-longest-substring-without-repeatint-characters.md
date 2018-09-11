---
title: 【Leetcode】3. Longest Substring Without Repeating Characters
date: 2018-09-11 21:58:34
tags: ['leetcode', 'javascript', '字符串']
---

> 题目链接：https://leetcode.com/problems/longest-substring-without-repeating-characters/description/

## 题目描述

给定一个字符串，找出不含有重复字符的最长子串的长度

## 输入输出

**示例1：**
- 输入："abcabcbb"
- 输出：3

**示例2：**
- 输入："bbbbb"
- 输出：1

## 思路

前几天做了一道通过滑动窗口解决道问题，这里依然借用这个思想。

首先有一个 map 对象，来存字符串中出现的单个字符最近的坐标（可以理解为最接近right的坐标）。如果 left 和 right 中出现了重复字符，则 left 指针移动道重复字符后一位，当 right 和 left 的距离大于 sum， 则 sum = right - left

## 代码

```javascript

/**
 * 时间复杂度 O(n)
 * 空间复杂度 O(n)
 */
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    var map = {};
    var left = sum = 0;
    var right = -1;
    
    while(right++ < s.length - 1 && left <= s.length) {
        // 如果当前字符在 无重复字符串中已存在，则 左指针从重复字符的下一位开始
        // 同时重复字符的坐标变更
        if(map[s[right]] && map[s[right]] >= left) {
            left = map[s[right]];
            map[s[right]] = right + 1;
        }
        // 如果不存在，则添加到 map 中
        else {
            map[s[right]] = right + 1;
            if(right - left + 1 > sum) {
                sum = right - left + 1;
            } 
        }
    }
    return sum;
};

```
