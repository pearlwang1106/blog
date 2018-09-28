---
title: 【学习笔记】lodash API - get
date: 2018-09-26 18:16:36
tags: ['javascript', 'lodash', '源码']
---

> 每日一言: 每天都超越自己, 请叫我王超越
> 参考文档: https://www.lodashjs.com/docs/4.17.5.html#get

# 写在前面

开发的时候看到项目中用到了 lodash 的 `_.get` api, 虽然知道 lodash 是开源的 js 工具库, 但是里面 api 很多, 并没有每个都去研究. 从 `_.get` 的 api 名字来看, 以为是异步请求接口什么的, 结果看了源码才发现完全不是这样.

# 简介

先看一下官方文档对这个 api 的介绍

- _.get(object, path, [defaultValue])
```html
Gets the value at path of object. If the resolved value is undefined,
the defaultValue is returned in its place.
```

翻译过来就是: 获取对象路径上的值, 如果返回的是 `undefined`, 则用默认值代替.

从文档中的介绍和示例并没有完全理解这个 api 的具体作用, 所以直接去看了一下源码, 下面对源码进行解读

# 源码

- 入口 get.js
```js
/* 入口 get.js */
function get(object, path, defaultValue) {
    var result = object == null ? undefined : baseGet(object, path);
    return result === undefined ? defaultValue : result;
}
```
入口代码很简单, 如果 result 是 undefined, 则返回默认值 `defaultValue`, 否则返回 result. 这里的关键是 `baseGet` 函数, 同时 `baseGet` 也有依赖, 所以下面把各个依赖函数解释下

- 依赖函数 baseGet
```js
/**
 * @file _.baseGet.js 
 * @function baseGet
 */
var castPath = require('./_castPath'),
    toKey = require('./_toKey');
function baseGet(object, path) {
    path = castPath(path, object);

    var index = 0, length = path.length;

    while (object != null && index < length) {
        object = object[toKey(path[index++])];
    }
    return (index && index == length) ? object : undefined;
}
```

    知道了下面每个函数的作用之后, 就能分析 baseGet 的逻辑:
    - 获取数组形式的 path(path = castPath(path, object))
    - 根据 path 循环查找
    - 最终返回结果对应的具体值
    - 举个栗子🌰

    ```js
    /**
     * 用官网文档中的栗子来说明 
     * var object = { 'a': [{ 'b': { 'c': 3 } }] };
     * _.get(object, 'a[0].b.c')  // => 3
     */

    // baseGet 函数
    // 1️⃣  可以获得 path = ['a', '0', 'b', 'c']
    path = castPath(path, object);

    // 2️⃣ while 循环过程可知 object 的变化过程
    /**
     * index = 0, object = object[path[0]] = [{ 'b': { 'c': 3 } }]
     * index = 1, object = object[path[1]] = { 'b': { 'c': 3 } }
     * index = 2, object = object[path[2]] = { 'c': 3 }
     * index = 3, object = object[path[3]] = 3
     * index = 4, 跳出
     */
    while (object != null && index < length) {
        object = object[toKey(path[index++])];
    }

    // get 函数
    // 所以主入口中 
    baseGet(object, path) // 3

    var result = object == null ? undefined : baseGet(object, path);
    // result = 3
    // 所以最终结论是 3
    ```
    
- 依赖函数 castPath
```js
/**
 * @file _castPath.js
 * @function castPath
 */
 var isArray = require('./isArray'), // 判断是否是对象
     isKey = require('./_isKey'),
     stringToPath = require('./_stringToPath'),
     toString = require('./toString'); // 转换成字符串

function castPath(value, object) {
  if (isArray(value)) {
    return value;
  }
  return isKey(value, object) ? [value] : stringToPath(toString(value));
}
```

    如果 value 是数组, 则直接返回数组, 否则如果 value 可以作为 key, 则返回 [value], 否则现将 value 转换成字符串, 再以数组的形式返回

- 依赖函数 toKey
```js
/**
 * @file _toKey.js
 * @function toKey
 */
var isSymbol = require('./isSymbol');
var INFINITY = 1 / 0;

function toKey(value) {
  if (typeof value == 'string' || isSymbol(value)) {
    return value;
  }
  var result = (value + '');
  return (result == '0' && (1 / value) == -INFINITY) ? '-0' : result;
}
```

    将参数 value 转换成可以做对象 key 的格式, 有如下几种情况:

    - value 的类型是 string 直接返回 value
    - value 是 symbol 直接返回 value
    - 不是前两种情况, 如果 value 是 0 , 则返回 '-0', 否则返回 value + '' 作为key

- 依赖函数 isKey
```js
var isArray = require('./isArray'),
    isSymbol = require('./isSymbol');

/** Used to match property names within property paths. */
var reIsDeepProp = /\.|\[(?:[^[\]]*|(["'])(?:(?!\1)[^\\]|\\.)*?\1)\]/,
    reIsPlainProp = /^\w*$/;

function isKey(value, object) {
  if (isArray(value)) {
    return false;
  }
  var type = typeof value;
  if (type == 'number' || type == 'symbol' || type == 'boolean' ||
      value == null || isSymbol(value)) {
    return true;
  }
  return reIsPlainProp.test(value) || !reIsDeepProp.test(value) ||
    (object != null && value in Object(object));
}
```

    从函数名可以知道 isKey 是判断一个 value 是否可以作为一个对象中的 key, 从代码中可以看出, 以下几种情况均是 true:
    
    - value 的类型是 `number`, `symbol` 或者 `boolean`
    - value 是null
    - value 是 symbol 值
    - value 是任意字母或数字或下划线或汉字组成的字符串
    - value 不是对象(!reIsDeepProp.test(value))
    - object 不是 null 并且 value 不是 object 的键值

- 依赖函数 stringToPath
```js
var memoizeCapped = require('./_memoizeCapped');

/** Used to match property names within property paths. */
var rePropName = /[^.[\]]+|\[(?:(-?\d+(?:\.\d+)?)|(["'])((?:(?!\2)[^\\]|\\.)*?)\2)\]|(?=(?:\.|\[\])(?:\.|\[\]|$))/g;

/** Used to match backslashes in property paths. */
var reEscapeChar = /\\(\\)?/g;

var stringToPath = memoizeCapped(function(string) {
  var result = [];
  if (string.charCodeAt(0) === 46 /* . */) {
    result.push('');
  }
  string.replace(rePropName, function(match, number, quote, subString) {
    result.push(quote ? subString.replace(reEscapeChar, '$1') : (number || match));
  });
  return result;
});
```
    虽然函数名为 stringToPath, 这里 path 其实是一个数组, 所以是将字符串转换为数组, 同时如果字符串第一个字符是 `.`, 则用空字符串代替


# 写在后面

所以 `_.get` 是根据传入的 path(可以是字符串或数组格式) 来获取对应的 object 中的值
