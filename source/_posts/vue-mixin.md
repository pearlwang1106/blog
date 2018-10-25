---
title: 【学习笔记】Vue mixin
date: 2018-10-25 19:03:52
tags: ['javascript', 'vue', 'mixin']
---

> 每日一言：有点无聊，我们来一起骂男人吧
> 参考文档：https://vuejs.org/v2/guide/mixins.html

## 定义

【摘自 vue 官网】混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

简单讲，混入就是讲一个数据对象中的属性合并到组件中，达到对可复用的属性，方法，功能等一个比较高的利用率。

### 举个 🌰

```js
/* 摘自官网 */
var myMixin = {
  created: function () {
    this.hello();
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!');
    }
  }
}

/* 调用 */
var Component = Vue.extend({
  mixins: [myMixin]
});
var component = new Component(); // => "hello from mixin!"
```
上述例子可以看出，首先定义了一个 mixin 对象 myMixin，对象中有两个 function，created 和 methods。然后通过 Vue.extend 将 myMixin 混入到组件 Component 中，也就是说 Component 组件用用了 created 和 methods 两个方法。此时通过 new 去创建一个组件实例的时候会自动执行这两个个函数。

## 规则

- 当组件和混入对象含有同名选项时，这些选项将以恰当的方式混合
```js
/* 摘自官网 */
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```
- 混入对象的钩子将在组件自身钩子之前调用
- 值为对象的选项，例如 methods, components 和 directives，将被混合为同一个对象。两个对象键名冲突时，取组件对象的键值对
- 也可以全局注册混入对象，一旦使用全局混入对象，将会影响到 所有 之后创建的 Vue 实例，需要恰当使用

## 混入策略

- 参考：https://vuejs.org/v2/guide/mixins.html#Custom-Option-Merge-Strategies
