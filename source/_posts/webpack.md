---
title: webpack v4+ 打包优化学习笔记
date: 2018-09-14 21:08:03
tags: ['javascript', 'webpack']
---

## 打包优化的几种方式

### 1. dll

对于一些比较大的库，可以先进性打包后在引入

### 2. happypack 多线程打包

启用多线程打包，js 是单线程，所以启用子进程打包

- 注意：项目比较小的时候，分配子进程所耗费的时候反而会增加打包时间，降低打包效率

```javascript
/* webpack.config.js */

```

### 3. tree-shaking

module 改成 production，此时打包过程中，没有 import 的function 不会被打包， 从容减少打包后文件的大小

- 注意 node exports.a 写法不会进行 tree-shakingstree-shakings

### 4. 避免重复打包

```javascript
output: {
    filename: [name].js,
},
optimization: {
    splitChunks: {
        cacheGroups: {
            common: {
                chunks: 'initial',
                minChunks: 2,
                minSize: 0
            },
            vendor: {
                test: /node_modules/, // 一般用来限制第三方插件多次打包
                chunks: 'initial',
                minChunks: 2,
                minSize: 0,
                priority: 10
            }
        }
    }
}
```

### 5. 

> 参考链接：https://webpack.docschina.org/plugins/module-concatenation-plugin
```
let ModuleConcatenationPlugin
```

### 6. 懒加载

> 参考文档：https://router.vuejs.org/guide/advanced/lazy-loading.html#grouping-components-in-the-same-chunk