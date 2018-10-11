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
    }
    return num;
}
```

## 层次遍历

```js
function getLeafNodes(node) {
    let num= 0;
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