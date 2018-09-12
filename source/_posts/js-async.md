---
title: 异步的顺序执行：promise, generator 以及 async/await
date: 2018-08-27 14:36:51
tags: ['javascript', 'promise', 'async/await', 'generator']
---

## 1. 名词解释

- Promise：简单讲就是通过链式调用解决了回调地域的问题
- Generator：Generator（迭代生成器）函数可以理解为是一个状态机，通过与函数内部的 `yield` 表达式配合使用达到使状态顺序执行的目的
- async/await：Generator 函数的语法糖

## 2. 示例说明

### 1) 问题说明

假如我们要加载3个文件，第一个加载完之后，才去加载第二个，第二个加载完之后，才去加载第三个，这里为了显而易见，通过进度条表示加载进度

**dom code**

```html
<!DOCTYPE html>
<html>
<head>
    <title>异步demo</title>
    <style type="text/css">
        .file1,
        .file2,
        .file3 {
            display: flex;
            margin: 20px auto;
        }
        .progress-bar-warpper {
            position: relative;
            display: inline-block;
            height: 22px;
            width: 400px;
            background: #c0c0c0;
        }
        .progress-bar {
            display: inline-block;
            height: 22px;
            width: 0;
            background-color: #ff6347;
        }
        .progress-num {
            position: absolute;
            left: 0;
            right: 0;
            top: 0;
            margin: auto;
            width: 25px;
        }
    </style>
</head>
<body>
    <section class="file1">
        <span>文件1：</span>
        <span class="progress-bar-warpper">
            <span class="progress-bar"></span>
            <span class="progress-num">0%</span>
        </span>
    </section>
    <section class="file2">
        <span>文件2：</span>
        <span class="progress-bar-warpper">
            <span class="progress-bar"></span>
            <span class="progress-num">0%</span>
        </span>
    </section>
    <section class="file3">
        <span>文件3：</span>
        <span class="progress-bar-warpper">
            <span class="progress-bar"></span>
            <span class="progress-num">0%</span>
        </span>
    </section>
</body>
</html>
```

### 2) promise 实现

Promise 可以用 es6 内置、自己开发或者第三方开源 Promise 库，下面的代码直接放到上面 dom 的尾部即可执行。

```javascript
<script type="text/javascript">
    let f1 = document.querySelector('.file1');
    let f2 = document.querySelector('.file2');
    let f3 = document.querySelector('.file3');

    // 进度条处理函数
    function Progress(dom) {
        return new Promise(function(resolve, reject) {
            var timer = setInterval(function() {
                var bar = dom.querySelector('.progress-bar');
                var num = dom.querySelector('.progress-num');
                var ow = bar.style.width;
                var cw = Number(ow.slice(0, ow.length - 1)) + 1;
                bar.style.width = Number(cw) + '%';
                num.innerHTML = bar.style.width;
                if(cw >= 100) {
                    clearInterval(timer);
                    resolve();
                }
            }, 50);
        });

    }

    // 链式调用保证顺序执行
    Progress(f1).then(function(data){
        return Progress(f2);
    }).then(function() {
        return Progress(f3);
    });
</script>
```

### 3) Generator 实现

生成函数要想达到异步顺序执行，依赖 [co 库](https://github.com/tj/co)（koa 里面用了大量的 generator），我们自己实现一个简单的 co 逻辑来实现我们想要的效果，非常粗暴，大概逻辑了解下即可。

```javascript
<script type="text/javascript">
    let f1 = document.querySelector('.file1');
    let f2 = document.querySelector('.file2');
    let f3 = document.querySelector('.file3');

    function Progress(dom) {
        return new Promise(function(resolve, reject) {
            var timer = setInterval(function() {
                var bar = dom.querySelector('.progress-bar');
                var num = dom.querySelector('.progress-num');
                var ow = bar.style.width;
                var cw = Number(ow.slice(0, ow.length - 1)) + 1;
                bar.style.width = Number(cw) + '%';
                num.innerHTML = bar.style.width;
                if(cw >= 100) {
                    clearInterval(timer);
                    resolve();
                }
            }, 50);
        });

    }

    function co(gen) {
        let ctx = this;
        let args = Array.prototype.slice.call(arguments, 1);

        return new Promise(function(resolve, reject) {
            if (typeof gen === 'function') {
                gen = gen.apply(ctx, args);
            }
            if (!gen || typeof gen.next !== 'function') {
                return resolve(gen);
            }

            onResolved();

            function onResolved(data) {
                var ret;
                try {
                    ret = gen.next(data);
                } catch (e) {
                    return reject(e);
                }
                next(ret);
                return null;
            }

            function onReject(err) {
                var ret;
                try {
                    ret = gen.throw(err)
                } catch (e) {
                    return reject(e);
                }
                next(ret);
            }

            function next(ret) {
                // 如果 done 是 true，则表示执行完成
                if (ret.done) {
                    return resolve(ret.value);
                }
                // 根据 Demo 的情况我们知道我们的 value 就是 promise，其他情况不做扩展，
                //  可以参考 co 源码库：https://github.com/tj/co
                if (ret.value instanceof Promise) {
                	// 通过 promise 的 then 实现异步顺序执行
                	// 同时 onResolved 和 onRejected 中再次调用 next 函数达到递归目的
                    return ret.value.then(onResolved, onReject);
                }
                return onRejected(new TypeError('type error'));
            }

        });

    }

    function* gen() {
        yield Progress(f1);
        yield Progress(f2);
        yield Progress(f3);
        return;
    }

    co(gen);
</script>
```


### 4) async/await 实现

async/await 是 generator 语法糖， ES2017 标准引入的，开发者就可以不在依赖 co 库了。

```javascript
<script type="text/javascript">
    let f1 = document.querySelector('.file1');
    let f2 = document.querySelector('.file2');
    let f3 = document.querySelector('.file3');

    function Progress(dom) {
        return new Promise(function(resolve, reject) {
            var timer = setInterval(function() {
                var bar = dom.querySelector('.progress-bar');
                var num = dom.querySelector('.progress-num');
                var ow = bar.style.width;
                var cw = Number(ow.slice(0, ow.length - 1)) + 1;
                bar.style.width = Number(cw) + '%';
                num.innerHTML = bar.style.width;
                if(cw >= 100) {
                    clearInterval(timer);
                    resolve();
                }
            }, 50);
        });

    }

    // 定义 async 函数，函数中用 await 保证 promise 的顺序执行
    async function start() {
        let l1 = await Progress(f1);
        let l2 = await Progress(f2);
        let l3 = await Progress(f3);
    }

    // 进入页面的时候开始执行
    start();

</script>
```

