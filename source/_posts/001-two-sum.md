---
title: 【LeetCode】1. two Sum
date: 2018-08-23 08:53:08
tags: ['leetcode', 'javascript']
---

> 题目连接: https://leetcode.com/problems/two-sum/description/

### 题目描述

- 输入: 整数数组 nums，目标和 target
- 输出: 输出 nums 中想加和为 target 的两个数的坐标索引

### demo

- 输入: [2, 7, 11, 15], 9
- 输出: [0, 1]   // 2 + 7 === 9

### 代码

```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    var result = [];
    for(var i = 0; i < nums.length; i++) {
        var index = nums.slice(i + 1).indexOf(target - nums[i]);
        if (index > -1){
            result.push(i);
            result.push(index + i + 1);
            delete index;
            break;
        }
    }
    return result;
};
```
