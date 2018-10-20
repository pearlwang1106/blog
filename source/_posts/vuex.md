---
title: 【学习笔记】Vuex
date: 2018-10-17 19:59:12
tags: ['javascript', 'vue', 'vuex']
---

> 每日一言：在这个声音吵杂的世界里相遇
> 参考文档：https://vuex.vuejs.org/zh/

## 什么是 Vuex

[摘自官网] Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

简单讲，是 Vue 的状态管理库。以一个单例模式管理组件的共享状态。

## 使用场景

- 一个中大型单页应用中
- 跨组件、跨页面的数据
- 跨复杂组件的数据

## 最简单的 store

```js
/* 摘自官网 */
const store = new Vuex.Store({
    state: {
        count: 0
    },
    mutations: {
        increment (state) {
        state.count++
        }
    }
});

/* 触发变更 */
store.commit('increment')

console.log(store.state.count) // -> 1
```

## 核心概念

说明：官网中介绍的很详细，本文作为学习笔记，简单记录学习过程中的重点

### State

> 单一状态树，可以认为 vuex 的所有状态都在 state 下面

#### 获取

- 单一状态
    - store.state.xxx
    - 子组件：this.$store.state.xxx
- 多状态
    - mapState 辅助函数，可以获取多个状态
    - ...mapState({}) 混入

### Getter

> 为了解决多个组件需要用到同一状态的情况，vuex 允许在 store 中定义 getter，接收 state 作为第一个参数，也可以接收其他 getter 作为第二个参数

#### 访问

- 通过属性访问
    - store.getters.xxx
    - 子组件：this.$store.getters.xxx
- 通过方法访问
- mapGetters 辅助函数：...mapGetters([])

### Mutation

> 更改 store 中状态的唯一方法。

```js
store.commit('yourFunction')
```

- 提交载荷
```js
store.commit('increment', 10)

store.commit('increment', {
  amount: 10
})

store.commit({
  type: 'increment',
  amount: 10
})

// 组件中提交
this.$store.commit('xxx')
// 或
...mapMutations([])
```

#### 注意

- 【建议】使用常量替代 Mutation 事件类型
- 【必须】Mutation 必须是同步函数

### Action

> 类似于 mutation。Action 提交的是 mutation，而不是直接变更状态，可以包含任意的异步操作

#### 分发

- store.dispatch('increment')
- 组件中分发
    - this.$store.dispatch('xxx')
    - ...mapActions([])
- 组合（结合 promsie）

### Module

> Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块


## 其他

其他关于项目结构、插件、测试等可以参考官网文档。