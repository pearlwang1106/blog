---
title: 【LeetCode】88. Merge Sorted Array
date: 2018-08-26 20:08:01
tags: ['leetcode', 'javascript', '数组']
---

> 题目连接: https://leetcode.com/problems/merge-sorted-array/description/

### 题目描述

- 输入: 有序数组 nums1, nums1 的长度 m, 有组数组 nums, nums2 的长度 n
- 输出: nums2 和 nums1 合并后的有序数组 nums1

### demo

- 输入: [1, 2, 3], 3, [2, 5, 6], 3
- 输出: [1, 2, 2, 3, 5, 6]

### 思路

- nums1 当前元素 <= nums2, nums1 指针后移
- nums1 当前元素 > nums2
	- nums1 当前元素与 nums2 当前元素交换
	- nums2 中当前元素向后比较，找到合适的位置，保证 nums2 是有序的
- 如果不是本题要求最终返回的还是 nums1，则可以用空间换时间的思路
	- 两个指针分别指向 nums1 和 nums2 的当前位置
	- nums1 和 nums2 中较小元素 push 到 c 数组中
	- 返回 c 数组

### 代码

```javascript
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function(nums1, m, nums2, n) {
    var i = 0;
    var j = 0;
    while(i < m && j < n) {
        if (nums1[i] > nums2[j]) {
            var t = nums1[i];
            nums1[i] = nums2[j];
            nums2[j] = t;
            
            for(var k = j; k < n; k++) {
                if (nums2[k] > nums2[k + 1]) {
                    t = nums2[k];
                    nums2[k] = nums2[k + 1];
                    nums2[k + 1] = t;
                }
            }
        }
        i++;
    }
    while(j<n) {
        nums1[i++] = nums2[j++];
    }
};
```
