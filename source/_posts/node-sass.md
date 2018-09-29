---
title: node 升级 sass 编译报错解决方案
date: 2018-09-28 13:03:43
tags: ['node', 'sass']
---

> 每日一言: 绿茶家的面包诱惑🍨真的好好吃👀

目前本地使用的 node 版本是 7.7.4, 据说太高版本的 node 在编译的时候回有 sass 报错, 所以尝试解决了一下

# 升级node  

```js
node stable

// node 切换版本  node 7.7.4
```

升级后对应的本地工具版本分别是:

- node 10.10.0
- npm 6.4.1
- cnpm 6.0.0

# 编译

- 项目中编译
```js
npm run build 编译

```

- 会发现报错:
```js
Found bindings for the following environments:

    OS X 64-bit with Node.js 7.
```

- 解决方法:
```js
npm rebuild node-sass
```
  
- 再次编译
```js
npm run build 编译 // 通过

```

- 可以对比升级前后产出目录diff
```js
diff -urNa 目录1 目录2
```
