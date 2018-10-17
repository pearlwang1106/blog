---
title: 输出一颗树的叶子节点个数
date: 2018-10-11 19:03:56
tags: ['javascript', '树', '数据结构']
---

> 每日一言: 我的木吉他

## 数据结构

```js
function Node(val) {
    this.val = val;
    this.children = [];
}
```

## 递归

```js
function getLeafNodes(node) {
    let num = 0;
    if(node) {
        if(node.children.length) {
            node.children.forEach(child => {
                num += getLeafsNum(child);
            });
        }
        else {
            num += 1;
        }
    }
    return num;
}
```

#### 举个栗子

<table style='margin-top:-550px'>
    <tr>
        <td><img src='/images/tree1.png' width='320px' height='252'></td>
        <td><img src='/images/tree2.png' width='320px' height='252'></td>
    </tr>
    <tr>
        <td><img src='/images/tree3.png' width='320px'></td>
        <td><img src='/images/tree4.png' width='320px'></td>
    </tr>
     <tr>
        <td><img src='/images/tree5.png' width='320px'></td>
        <td><img src='/images/tree6.png' width='320px'></td>
    </tr>
     <tr>
        <td><img src='/images/tree7.png' width='320px'></td>
        <td><img src='/images/tree8.png' width='320px'></td>
    </tr>
     <tr>
        <td><img src='/images/tree9.png' width='320px'></td>
    </tr>
</table>

## 层次遍历

```js
function getLeafNodes(node) {
    let num = 0;
    if(node) {
        let queue = [];
        queue.push(node);
        while(queue.length) {
            let p = queue.pop(); // 取出队列第一个元素
            if(p.children.length) {
                p.children.forEach(child => {
                    queue.push(child);
                }
            }
            else {
                num++;
            }
        }
        delete queue;
    }
    return res;
}
```

#### 举个栗子

层次遍历需要借助额外的空间，需要一个队列进行存储。同样是上面的树，通过层级遍历的过程如下所示，最后保留一个空方框是为了说明队列已空。

<table>
    <tr>
        <td><img src='/images/tree10.png' width='320px'></td>
        <td>
            <img src='/images/tree11.png' width='100px' style='margin: 0'>
            <img src='/images/tree12.png' width='164px' style='margin: 0'>
            <img src='/images/tree13.png' width='130px' style='margin: 0'>
            <img src='/images/tree14.png' width='130px' style='margin: 0'>
            <img src='/images/tree15.png' width='200px' style='margin: 0'>
            <img src='/images/tree16.png' width='153px' style='margin: 0'>
            <img src='/images/tree17.png' width='120px' style='margin: 0'>
            <img src='/images/tree18.png' width='135px' style='margin: 0'>
            <img src='/images/tree19.png' width='110px' style='margin: 0'>
            <img src='/images/tree20.png' width='105px' style='margin: 0'>
        </td>
    </tr>
</table>
