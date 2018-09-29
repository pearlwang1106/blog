---
title: 变量作用域：var 和 let
date: 2018-09-25 20:57:51
tags: ['javascript', '面试']
---

## 说明

ES5 中 var 定义的变量会做变量提升（提升至当前作用域），ES6 中的 let 不会做变量提升，但是会造成块级作用域的暂时性死区

> 参考：http://es6.ruanyifeng.com/#docs/let

## 问题

有如下代码，请写出输出结果：

```javascript
var a = 1;
var b  = 2;
function F () {
    console.log(this.a);
    console.log(a);
    console.log(this.b);
    console.log(b);
    let a = 3;
    var b = 4;
    return function () {
        console.log(this.a);
        console.log(a);
        console.log(this.b);
        console.log(b);
    }
}

var d = F();
d();
```

根据 var 以及 let 的特型，对上面的代码进行转换（同时标记每行输出）：

```javascript
var a = 1;
var b  = 2;
function F () {
    var b = undefined;
    console.log(this.a); // 1
    console.log(a);      // ReferenceError  有 let 暂时性死区导致
    console.log(this.b); // 2
    console.log(b);      // undefined
    let a = 3;
    var b = 4;
    return function () {
        console.log(this.a); // 1
        console.log(a);      // 3
        console.log(this.b); // 2
        console.log(b);      // 4
    }
}

/* 此种调用方式，非严格模式下，F() 中的 this 会找其上级，所以最终指向 window */
var d = F();
d();

```