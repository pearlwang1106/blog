---
title: 【Leetcode】189. Rotate Array
date: 2018-08-29 20:02:07
tags: ['leetcode', 'javascript', '数组']
---

> 题目链接：https://leetcode.com/problems/rotate-array/description/

## 题目描述

给出一个数组和一个数字 k，将数组的后 k 个数字移动到数组前面

## 输入输出

**示例1：**
- 输入：nums = [1,2,3,4,5], k = 2
- 输出：[4,5,1,2,3]

**示例2：**
- 输入：nums = [1,2,3,4,5,6], k = 3
- 输出：[4,5,6,1,2,3]

## 思路

**方法1：**
- 如果每个数字当前所在的位置是 curr，那么他移动后的位置则是 (curr + k) % nums.length
- 所以可以每个数字移动到他的下一位，然后把下一位数字继续移动到下一位
- 上面的例子的移动过程可以描述如下：
    - 示例1：1 -> 3, 3 - > 5, 5 -> 2, 2 -> 4, 4 -> 1
    - 示例2：
        - 1 -> 4, 4 -> 1
	- 2 -> 5, 5 -> 2
	- 3 -> 6, 6 -> 3

**方法2：**
- 首先将整个数组倒叙
- 前 k 个数字再倒叙
- 后 nums.length - k 个数字倒叙
- 上面的例子的移动过程：
    - 示例1：
	    - [5,4,3,2,1]
	    - [4,5,3,2,1]
	    - [4,5,1,2,3]
    - 示例2：
	    - [6,5,4,3,2,1]
	    - [4,5,6,3,2,1]
	    - [4,5,6,1,2,3]

## 代码

**方法1：**

```javascript

/**
 * 时间复杂度：O(n)
 * 空间复杂度：O(1)
 */
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    var len = nums.length;
    k = k % nums.length;

    // 用于标记当前已经找到正确位置的数字的个数
    var cnt = 0;        
    for(var i = 0; cnt < len - 1; i++) {
        var curr = i;                   // 指向当前将要移动的数字的坐标
        var next = (i + k) % len;       // 当前数字应该移动到的地方的坐标
        var prev = nums[i];             // 上一位数字
        do {
            var tmp = nums[next];
            nums[next] = prev;
            prev = tmp;
            curr = next;
            next = (next + k) % len;
            cnt ++;
        } while(i !== curr);
    } 
};
```

```javascript
/**
 * 时间复杂度：O(2n) = O(n)
 * 空间复杂度：O(1)
 */
var reverse = function(array, st, ed) {
    for(var i = st; i < (st + ed) / 2; i++) {
        var tmp = array[ed + st - i - 1];
        array[ed + st - i - 1] = array[i];
        array[i] = tmp;
    }
    return array;
};
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    k = k % nums.length;
    nums = reverse(nums, 0, nums.length);
    nums = reverse(nums, 0, k);
    nums = reverse(nums, k, nums.length);
};
```
