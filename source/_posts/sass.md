---
title: Sass 学习
date: 2018-09-13 15:24:54
tags: ['sass']
---

> 参考文档: https://www.sass.hk/docs/

sass 可以认为是 css 的预处理, 在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能，这些拓展令 CSS 更加强大与优雅. 开源库[compass](http://compass-style.org/) 中就是基于 sass 完成的一个非常丰富的样式框架，包括大量定义好的mixin，函数，以及对SASS的扩展

## sass 主要特点

- 支持变量
    - 变量作用域
    - 变量命名中线和下划线不做区分($custom-name 和 $custom_name 是一样的)

```css
$width: 5em;
#main {
  width: $width;
}

/* 编译后 */
#main {
  width: 5em;
}
```
- 允许嵌套
    - 父选择器用 & 表示
    - 属性嵌套
    - 群组嵌套
- `//` 注释编译后不会在 css 中显示
- 支持命令行操作(SassScript)
- 支持导入文件(@import)
    - sass(如果不想导入文件被编译为css, 则以下划线开头命名, 导入时不用加下划线, 如下例)
    - scss
    - css

```css
/* 文件 _colors.scss */

@import colors
```
- 支持继承
    - 多重继承
    - 链式继承
- 支持控制指令 (@if @else 等)
- 支持混合器(@mixin) (用于定义可重复使用的样式，避免了使用无语意的 class)
- 支持函数

这里只是个人的学习笔记, 详细使用可以参考: https://www.sass.hk/docs/, 后续使用过程中如果遇到觉得比较重点的问题会继续更新
