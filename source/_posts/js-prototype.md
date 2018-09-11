---
title: 原型及原型链的简单理解
date: 2018-08-23 10:43:31
tags: ['javascript', '原型', '面试']
---

面试的时候被到原型的相关问题，回来后再次学习了一下原型及原型链，在这里做个总结。

**问题**

```
case1:

function F() {}
var f = new F();

case2:

function F() {return '123';}
var f = new F();

case3:

function F() {return {a:1};}
var f = new F();

```

这两种情况，f 的原型是如何指向的？

**理解**

想要知道 f 的原型指向，需要先了解 f 具体是什么。下面先说一下经过上述步骤后 的具体值分别是什么。

首先，通过 new 来调用函数或者方法的方式，是`构造函数调用`。有三种情况：

1. 构造函数不使用 return 关键字，这种情况下，会初始化一个新的对象，这个对象继承自构造函数的 prototype 属性；当构造函数的执行体执行完毕时，会返回这个新对象。此时，构造函数调用表达式的计算结果就是这个新对象的值，如问题中的 case1。
2. 构造函数显式的使用了 return 关键字，返回了一个原始值，如问题中的 case2；这种情况下，仍然会初始化一个新的对象，这个对象继承自构造函数的 prototype 属性，return 的返回值将会被忽略，同时使用新对象作为调用结果。
3. 构造函数显式的使用了 return 关键字，并且返回了一个对象，如问题中的 case3，这种情况下，调用表达式的值就是这个对象。

如果问题描述不是很清楚，可以从代码层面理解一下，new 过程的代码实现：

```
function new() {
    // 创建一个空的对象
    let obj = new Object()
    // 获得构造函数
    let Con = [].shift.call(arguments)
    // 链接到原型
    obj.__proto__ = Con.prototype
    // 绑定 this，执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return typeof result === 'object' ? result : obj;
}
```

从代码也可以看出，new 的过程最后会判断函数中 return 的是否是 object，不是的话才会返回一个新的对象

所以上述问题中的三种情况，结果分别是：

```
case1:
console.log(f);  // F {}

case2:
console.log(f);  // F {}

case3:
console.log(f); // {a: 1}

```

所以，根据原型链可知

```
case1:

f.__proto__ === F.prototype;
F.__proto__ === Function.prototype;
F.prototype.__proto__ === Object.prototype // 原型对象也是对象

case2:

同 case1

case3:

f.__proto__ === Object.prototype
F.__proto__ === Function.prototype
F.prototype.__proto__ === Object.prototype // 原型对象也是对象

```

- 参考文档：
	- https://github.com/KieSun/Blog/issues/2
	- javascript 权威指南



