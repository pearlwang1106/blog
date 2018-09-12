---
title: Git submodule (Git 子模块)
date: 2018-09-12 11:48:58
tags: ["Git"]
---

> 参考文档: https://git-scm.com/book/en/v2/Git-Tools-Submodules

在学习的过程中, 暂时觉得 git submodule 和 npm packege 维护方式都是可以的, 后面熟悉后再对两种方式的优缺点进行分析.

## 使用场景

多个父项目中都要使用到子项目 son.

#### 现有解决方案及弊端

- CPAN 安装或 Ruby gem. 弊端: 暂时不了解, 后续补充
- 拷贝源代码到父项目中. 弊端: 本地修改 son 代码后, 其他父项目中不能及时同步
- npm package 依赖形式引入. 弊端: 需要加版本所, 依赖包升级需要发布(不过这里和 git submodule 两个优缺点对比, 后续在熟悉后再补充)

#### Git 解决方案

- git submodule (git 会把子项目看做子模块)

## Git submodule 使用介绍

#### 1. 添加 submodule

```javascript
git submodule add https://github.com/son   // 这里是假设的地址

或
// 指定目录
git submodule add https://github.com/son pathTo
```

添加后会在当前目录下生成一个配置文件 `.gitmodules`, 配置文件中有所有 submodule 的相关配置, 多个子项目的配置会根据添加顺序排列. 如:

```json
[submodule "son"]
	path = son
	url = https://github.com/son
```

#### 2. clone 子模块项目

```javascript
/* --recursive 会自动的拉去子项目并对其进行更新 */
git clone --recursive father // 假设父项目叫 fathe

或

/* clone 父项目后, 进入到项目目录, 初始化子项目并更新子项目 */
git clone father
cd father
git submodule init
git submodule update
```

#### 3. update 子模块项目

```javascript
/* 进入到 submodule 中手动更新 */
cd son
git fetch
git merge origin/master

或

git submodule update --remote son
```

#### 4. 查看 子模块项目

```javascript
git diff --submodule

或添加配置

git config --local diff.submodule log // --global 也可以, 这里测试, 只修改了 local
git diff
```

#### 5. 子模块项目指向其分支

```javascript
git config -f .gitmodules submodule.son.branch dev

/* 查看 .gitmodules 文件可看到如下*/
[submodule "son"]
    path = son
    branch = dev
	url = https://github.com/son
```

## 如何开发子项目

当我们在 father 中进行开发的时候, 虽然可以直接拉取最新的 submodule 代码, 但是 git 并不会对其进行跟踪. 所以, 如果要修改子项目代码, 需要进入到子项目目录中.

#### 1. 切换到开发分支

```javascript
git checkout stable
```

#### 2. 将远程分支合并到当前分支

```javascript
/**
 *  类似 fork 工作流
 */
git submodule update --remote --merge

/* 或 */
git submodule update --remote --rebase
```

#### 3. push 代码

```javascript
/* 提交 father 的时候加如下参数, 可以检测未提交的 submodule  */
git push --recurse-submodules=check

/* 自动提交指定 submodule */
git push --recurse-submodules=on-demand
```
