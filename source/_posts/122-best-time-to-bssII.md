---
title: 【Leetcode】122. Best Time to Buy and Sell Stock II
date: 2018-08-30 20:20:07
tags: ['leetcode', 'javascript', '数学']
---

> 题目链接：https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/

## 题目描述

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。可以进行多次买卖，但是比如在卖出之后才能再次买入

## 输入输出

**示例1：**

- 输入: [7,1,5,3,6,4]
- 输出: 7
- 解释: 第二天买入（价格 1），第三天卖出（价格5），获得收益 5 - 1 = 4；第四天买入（价格3），第四天卖出（价格6），收益 6 - 3 = 3；总收益 4 + 3 = 7

**示例2：**

- 输入: [1,2,3,4,5]
- 输出: 4
- 解释: 第一天买入（价格1），第五天卖出（价格5），总收益 5 - 1 = 4

## 思路

一遍循环，价格下降的时候找出最合适的买入价；价格上升的时候找出最合适的卖出价，卖出和买入价格都有值的时候计算出当前收益

## 代码

```javascript
/**
 * 时间复杂度：O(n)
 * 空间负责度：O(3) = O(1)
 */
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
    var sum = 0;
    var buy = prices[0];
    var sale = -1;
    for(var i = 1 ; i < prices.length; i++) {
        if(prices[i] < prices[i - 1]) {
            // 如果走势开始下降，并且之前已经有最大值和最小值
            // 则计算出之前买入和卖出的最大收益
            if(sale !== -1) {
                sum += sale - buy;
                sale = -1;
            }
            buy = prices[i];
        }
        else if(prices[i] > prices[i - 1]) {
            sale = prices[i];
        }
    }
    // 如果价格一直上升，则最后卖出，计算收益
    if(sale !== -1) {
       sum += sale - buy;
    }
    return sum;
};
```


