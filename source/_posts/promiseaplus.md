---
title: promise A+ 规范解读及轻量级实现
date: 2018-08-25 13:09:13
tags: ['javascript', 'promise']
---

> promise A+ 规范文档：https://promisesaplus.com/

之所以要遵循 promise A+ 规范，是由于开源以及开发中单独写的 promise 库太多了，为了每种 promise 库可以相互兼容或调用，所以要遵守规范。

## demo 代码

笔者自己根据 promise 规范写了一个[轻量级 promise](https://github.com/pearlwang1106/promise)

下面对 promise A+ 规范进行解读

## 1. 术语

1. `promise` 是一个有 `then` 方法的符合 promise A+ 规范的对象或者函数
2. `thenable` 是定义了then 方法的对象或函数
3. `value` 是任何Javascript值（比如 undefined，一个 thenable 或一个 promise 等）
4. `exception` 是通过 throw 抛出的表达式的值
5. `reason` 是一个用于描述 Promise 被拒绝原因的值

## 2. 规定

### 2.1 promise 状态

一个 promise 只有**三种**状态：pending, fulfilled, 或 rejected

#### 2.1.1 promise 是 pending 状态：

- 2.1.1.1 既可以转换成 fulfilled 状态，也可以转换成 rejected 状态

#### 2.1.2 promise 是 fulfilled 状态：

- 2.1.2.1 不能转换成其他状态
- 2.1.2.2 必须有一个不可以改变(===)的 `value`

#### 2.1.3 promise 是 rejected 状态：

- 2.1.3.1 不能转换成其他状态
- 2.1.3.2 必须有一个不可以改变(===)的 `reason`

### 2.2 then 方法

一个 promise 必须有一个 then 方法用来返回这个 promise 当前或最终的 `value`，或者拒绝的 `reason`。

一个 promise 的 `then` 方法有两个入参：

```
promise.then(onFulfilled, onRejected)
```

#### 2.2.1 onFulfilled 和 onRejected 都是可选参数

- 2.2.1.1 如果 `onFulfilled` 不是一个函数，那么它就必须被忽略。
- 2.2.1.2 如果 `onRejected` 不是一个函数，那么它就必须被忽略。

#### 2.2.2 如果 `onFulfilled` 是一个函数

- 2.2.2.1 必须在 promise 完成后调用，同时用 promise 的 `value` 作为他的第一个参数
- 2.2.2.2 严禁在 promise 完成之前调用
- 2.2.2.3 最多只能调用一次

#### 2.2.3 如果 `onRejected` 是一个函数

- 2.2.3.1 必须在 promise reject 后调用，同时用 promise 的 `reason` 作为他的第一个参数
- 2.2.3.2 严禁在 promise reject 之前调用
- 2.2.3.3 最多只能调用一次

#### 2.2.4 严禁在当前执行上下文（[execution context](https://es5.github.io/#x10.3)）中调用 `onFulfilled` 或 `onRejected` [3.1]

#### 2.2.5 【必须】`onFulfilled` 或 `onRejected` 必须是函数
#### 2.2.6 同一个 promise 可以多次调用 `then` 方法

- 2.2.6.1 当 promise 执行完成，所有的 `onFulfilled` 都必须按照其注册顺序执行
- 2.2.6.2 当 promise 被拒绝，所有的 `onRejected` 都必须按照起注册顺序执行

#### 2.2.7 `then` 必须返回一个新的 promise[3.3]

```
promise2 = promise1.then(onFulfilled, onRejected);
```

- 2.2.7.1 当 `onFulfilled` 或 `onRejected` renturn 一个 x，则执行 Promise 解析程序 [[Resolve]](promise2, x)
- 2.2.7.2  当 `onFulfilled` 或 `onRejected` 抛出了一个异常 e，promise2 应当以 e 为 `reason` 被拒绝
- 2.2.7.3 如果 `onFulfilled` 不是一个函数并且 promise1 已经 fulfilled，则 promise2 必须以 promise1 的值 fulfilled
- 2.2.7.4 如果 `onRejected` 不是一个函数且 promise1 已经 rejected, 则 promise2 必须以相同的 reason 被拒绝

### 2.3 promise 解析程序

Promise 解析过程 是以一个 promise 和一个值做为参数的抽象过程，可表示为[[Resolve]](promise, x)。解析过程如下：

#### 2.3.1 如果 x 和 promise1 相等，则跑错一个 TypeError
#### 2.3.2 如果 x 是一个 promise，则根据其状态进行处理：

- 2.3.2.1 如果 x 是 pending 状态，则直到 x fulfilled 或 reject 后才能改变状态
- 2.3.2.2 如果 x 是 fulfilled 状态，将 x 的值用于 fulfill promise
- 2.3.2.3 如果 x 是 rejected 状态，将x的原因用于reject promise

#### 2.3.3 如果 x 是一个对象或一个函数

- 2.3.3.1 `let then = x.then` [3.5]
- 2.3.3.2 如果在取 x.then 的值的时候抛出了一个异常 e，则 reject promsie，并且 reason 是 e
- 2.3.3.3 如果 then 是一个函数，以 x 为 this 调用 then 函数，并且第一个参数是 resolvePromise，第二个参数是 rejectPromise：
    - 2.3.3.3.1 当 resolvePromise 被以 y 为参数调用, 执行 [[Resolve]](promise, y)
    - 2.3.3.3.2 当 rejectPromise 被以 r 为参数调用, 则以 r 为原因将 promise 拒绝
    - 2.3.3.3.3 如果 resolvePromise 和 rejectPromise 都被调用了，或者被调用了多次，则只第一次有效，后面的忽略
    - 2.3.3.3.4 如果在调用then时抛出了异常，则：
	    - 2.3.3.3.4.1 如果 resolvePromise 或 rejectPromise 已经被调用了，则忽略
	    - 2.3.3.3.4.2 否则, 以 e 为 reason 将 promise 拒绝
- 2.3.2.4 如果 then 不是一个函数，则 以 x 为值 fulfill promise。

#### 2.3.4 如果 x 不是对象也不是函数，则以x为值 fulfill promise。

---

> 上述则是 promise A+ 规范中必须的规范，其他可以参考原英文文档，如有笔误可联系笔者进行更正
> 近期实现完轻量级 promise 后会在更新此文




