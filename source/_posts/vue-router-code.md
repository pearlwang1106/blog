---
title: 【学习笔记】vue-router 使用
date: 2018-09-27 12:54:18
tags: ['javascript', 'vue', 'vue-router']
---

> 参考文档: https://router.vuejs.org/zh/
>
> vue0router 源码: https://github.com/vuejs/vue-router

# 简介

Vue Router 是 [Vue.js](https://cn.vuejs.org/) 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。[官方介绍]]

# 使用

### DOM API

- router-link

`router-link` 有很多属性, 比如 `to`, `replace` 和 `tag` 等, 可以[参考文档 router-link-props](https://router.vuejs.org/zh/api/#router-link-props) 一节

```javascript
    <router-link tag="li" to="/foo"></router-link>
```

- router-view

router-view 是一个 functional 组件，渲染路径匹配到的视图组件, 可以简单的理解为将路由对应的组件渲染在 router-view 中. 

可以[参考官方效果展示](https://jsfiddle.net/6du90epg/7730/).

分别点击右下角的 '/' 和 '/other', 可以看到页面中渲染了不同的视图

主要原理是通过 `<router-view>` 的 name 属性, 配置了渲染的组件name

然后在相应的 path 下配置将组件 name 映射到实际的组件

```javascript
/* 此段代码从官方效果展示复制过来, 主要为了解释下 router-view 工作 */
/* html 部分 */
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

<div id="app">
  <h1>Named Views</h1>
  <ul>
    <li>
      <router-link to="/">/</router-link>
    </li>
    <li>
      <router-link to="/other">/other</router-link>
    </li>
  </ul>
  <router-view class="view one">hhh</router-view>
  <router-view class="view two" name="a"></router-view>
  <router-view class="view three" name="b"></router-view>
</div>

/* js 部分 */
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
const Baz = { template: '<div>baz</div>' }

const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '/',
      // a single route can define multiple named components
      // which will be rendered into <router-view>s with corresponding names.
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      },                                                                                                                                               
    },
    {
      path: '/other',
      components: {
        default: Baz,
        a: Bar,
        b: Foo
      }
    }
  ]
})

new Vue({
	router,
  el: '#app'
})

```

### Router 构建选项

一般在一个项目中会通过如下方法构建路由, 详细构建参数参考[官网-Router 构建选项](https://router.vuejs.org/zh/api/#router-%E6%9E%84%E5%BB%BA%E9%80%89%E9%A1%B9) 一节.

```js
    new Router({
        routes: routes,
        modes: modes
        ...
    });
```

这里 `routes` 最为常用, 是一个对象数组, 每个字段解释如下:

```js
routes = [
    {
        /**
         * {string} 必须 路由
         */
        path: '',
        /**
         * {Component} 非必须 视图组件
         */
        component: '',
        /**
         * {string} 非必须 明明路由
         */
        name: '',
        /**
         * {Object} 非必须 命名视图组件
         */
        components: { [name: string]: Component };
        /**
         * {string/Location/Function} 非必须 重定向逻辑
         */
        redirect?: string | Location | Function;
        /**
         * {boolean/string/Function} 非必须 路由属性
         */
        props?: boolean | string | Function;
        /**
         * {string/Array} 非必须 别名
         */
        alias?: string | Array<string>;
        /**
         * {Array<RouteConfig>} 非必须 嵌套路由
         */
        children?: Array<RouteConfig>;
        /**
         * 非必须
         */
        beforeEnter?: (to: Route, from: Route, next: Function) => void;
        /**
         * {any} 非必须 感觉像是一些业务相关配置
         */
        meta?: any;

        /**
         * {boolean} 非必须 匹配规则是否大小写敏感？(默认值：false)
         * 2.6.0+
         */
        caseSensitive?: boolean;
        /**
         * {Object} 非必须 编译正则的选项
         * 2.6.0+
         */
        pathToRegexpOptions?: Object;
    }, {
        ...
    }
]
```

### Router 实例属性和实例方法

Router 的实例属性和实例方法, [官方文档](https://router.vuejs.org/zh/api/#router-%E5%AE%9E%E4%BE%8B%E5%B1%9E%E6%80%A7)介绍的很详细, 这里不做过多讲解, 

直接参考官方文档即可.