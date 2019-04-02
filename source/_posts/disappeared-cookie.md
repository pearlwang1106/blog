---
title: 消失的 Cookie
date: 2019-04-02 16:56:57
tags: ['http', 'cookie']
---

> 每日一言: 一个人的行走范围，就是他的世界

## 背景

公司项目要进军移动端微信公众号，于是把 pc 部分逻辑迁移到移动端并进行升级。登录相关逻辑和 pc 一致，本来我在极速的 happy hacking，突然一个和 pc 一样的请求接口 503 了。

## 追踪

- 第一步：
    想想之前接口 503 一般是少了什么参数，导致后端转发的时候没带过去，所以 503，所以就监查了一下前端请求接口的 request headers. request headers 下面显示了一个黄色的⚠️，后面跟着：Provisional headers are shown。同时这个 request headers 的 header 参数并不完整，很明显的没有 cookie，就想着是不是由于没有取到 cookie 所以导致参数不完善才 503。但是 pc 网页的同一个接口请求就没有这个问题。

- 第二步：定位 cookie 丢失的原因
    1. 移动端页面与pc端页面域名不同，导致跨域（pass）
    2. cookie 设置了path，只允许设置的path下的路径访问（查看cookie的path 是'/', pass）
    3. 页面代码写法有问题（注释页面代码，直留一个 ajax 请求，同样有问题，pass）
    4. 确定 Provisional headers are shown 的原因
        - 在浏览器中输入chrome://net-internals/#events，刷新页面重新请求，搜索delegate_blocked_by这个关键字（打开 chrome://net-internals/#events 发现需要下载日志文件到本地，与网上查找的wiki说明不同，不过尝试后本地日志看起来不方便，未能定位问题）
        - 很多篇wiki都说是chrome 插件引起的，所以用 safari 打开连接发现 request headers 参数正常，于是挨个关掉插件，再打开，神奇的是居然没问题了

## 结论

不过最后，接口503 还是没有结论，需要rd同学帮忙定位，但是目前能定位到是后端接口的问题了。