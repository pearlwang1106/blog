---
title: 【学习笔记】Thunk 函数
date: 2018-10-19 16:28:43
tags: ['javascript', 'generator']
---

> 每日一言：绅士就是所做的不是自己想做之事，而是自己应做之事 - 村上春树

从阮一峰的《ES6 标准入门》了解到 Thunk 函数的概念，为什么百度翻译 Thunk 是 臀部 😓 。

## 背景

### 举个 🌰

```js
var x = 1;
function f(m) {
    return m * 2;
}

f(x + 5);
```

编程语言对于上述程序的求值策略有两种意见，即『传值策略（call by value）』和『传名调用（call by name）』。

- 传值调用：先计算出 x + 5(=6)， 再调用 f(6)
    - 优点：简单
    - 缺点：某些参数没用到就计算，会造成性能损失
- 传名调用：直接将表达式 x + 5 传入函数体，即 (x + 5) * 2

## 含义

> 【摘自阮一峰的《ES6 标准入门》】编译器的“传名调用”的实现往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就成为 Thunk 函数。

### Javascript 的 Thunk 函数

> JS 语音是传值调用，所以在 JS 中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只接受毁掉函数作为参数的单参数函数，如下例：

```
f(a, cb);

var Thunk = function (a) {
    return function (cb) {
        return f(a, cb); 
    };
};

var ex = Thunk(a);
ex(cb);
```

可以看出，对于一个有多个参数的函数（有一个参数是毁掉函数），Thunk 函数的作用就是将其转换成一个只接收回调函数作为参数的单参数函数。

## thunkify 模块

> 源码：https://github.com/tj/node-thunkify
> npmjs：https://www.npmjs.com/package/thunkify

thunkify 是一个可以直接拿过来用的封装好的 Thunk 函数，源码很简单，限制了回调函数只允许执行一次。和 co 一样是 tj 开发的，好吧，tj已经是我偶像了。

## 作用

书中讲，以前是没什么用的，因为没有什么使用场景，但是 ES6 中有了 Generator 函数之后， Thunk 函数就可以用于 Generator 函数的自动流程管理。

### 举个 🌰

```js
function* gen() {
    // ...
}

var g = gen();
var res = g.next();

while(!res.done) {
    console.log(res.value);
    res = g.next();
}
```

上述情况下，while 循环会一直自动执行 gen.next 直到完成所有的步骤。这样有一个缺点是，在异步下，需要等前一个异步操作执行完之后再继续执行。此时，就需要借助 Thunk 函数。举一个书中的栗子：

```js
/* 以读取文件为例 */

var fs = require('fs');
var thunkify = require('thunkify');
var readFileThunk = thunkify(fs.readFile);

var gen = function* () {
    var r1 = yeild readFileThunk('/etc/fstab');
    console.log(r1.toString());
    var r2 = yeild readFileThunk('/etc/shells');
    console.log(r2.toString());
};
```
众所周知，Generator 函数中的 yeild 是交出 Generator 函数的执行权，即去执行异步函数，而 yeild 后面跟的 Thunk 函数可以在回调函数中，将执行权重新交给 Generator。

此时，可以通过一个 Generator 函数的自动执行器来自动执行 Generator 函数。如下：

```js

function run(fn) {
    var gen = fn();

    function next(err, data) {
        var result = gen.next(data);
        if (result.done) {
            return;
        }
        result.value(next);
    }

    next();
}

function* g() {
    // ...
}

// 执行
run(g);
```