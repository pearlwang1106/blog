---
title: 【LeetCode】Single Number I && II && III
date: 2018-08-24 13:24:33
tags: ['leetcode', 'javascript', '整数', '去重']
---

> 题目连接: 
> I:   https://leetcode.com/problems/single-number/description/
> II:  https://leetcode.com/problems/single-number-ii/description/
> III: https://leetcode.com/problems/single-number-iii/description/

### 题目描述

**Single Number I**: 给一个整数数组，数组中只有一个整数只出现一次，其他的整数分别出现两次

- 输入：[4, 1, 2, 1, 2]
- 输出：4

**Single Number II**: 给一个整数数组，数组中只有一个整数只出现一次，其他的整数最少现两次

- 输入：[2, 2, 3, 2]
- 输出：3

**Single Number III**: 给一个整数数组，求出数组中只出现一次的数组结果

- 输入：[1, 2, 1, 3, 2, 5]
- 输出：[3, 5]

### 思路

**Single Number I**: 最简单的方法是通过位运算，一次遍历即可
**Single Number II**: 

- 第一种：从第一个元素开始向后找，找到所有重复的元素置空，双重循环时间复杂度要 O(n^2)
- 第二种：空间换时间，一遍循环将每个数字出现的次数存到一个对象中（因为有负数的情况，所以用数组），最后将次数为 1 的返回

**Single Number I**: 在 II 的基础上，返回数组即可

### 代码

**Single Number I**:

```
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    var r = 0;
    for(i = 0; i < nums.length; i++) {
        r = r ^ nums[i];
    }
    return r;
};
```

**Single Number II**:

```
/**
 * 
 * 第一种
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    var f;
    for(var i = 0; i < nums.length; i++) {
        f = 0;
        for(var j = i + 1; j < nums.length; j++) {
            if (nums[j] === nums[i]) {
                nums[j] = null;
                f = 1;
            }
        }
        if (f === 1) {
                nums[i] = null;
            }
    }
    for(var i = 0; i < nums.length; i++) {
        if (nums[i] !== null) {
            return nums[i]
        }
    }
    
};


/**
 * 第二种
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    var a = {};
    for(var i = 0; i < nums.length; i++) {
        if(!!a[nums[i]]) {
            a[nums[i]] += 1;
        }
        else {
            a[nums[i]] = 1;
        }
    }
    for(var i = 0; i < nums.length; i++) {
        if(a[nums[i]] === 1) {
            delete a;
            return nums[i];
        }
    }
    
};
```

**Single Number III**:

```
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var singleNumber = function(nums) {
    var a = {};
    var r = [];
    for(var i = 0; i < nums.length; i++) {
        if(!!a[nums[i]]) {
            a[nums[i]] += 1;
        }
        else {
            a[nums[i]] = 1;
        }
    }
    for(var i = 0; i < nums.length; i++) {
        if(a[nums[i]] === 1) {
            r.push(nums[i]);
        }
    }
    delete a;
    return r;
};
```
