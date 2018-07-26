---
layout: post
title:  "在Node中使用ES Modules"
date:   2017-08-17
categories: node.js
author: 张翔
location: ShangHai, China
cover: "https://i.loli.net/2018/07/24/5b57414660673.jpeg"
description: 我的翻译计划第二篇，这篇主要介绍的Node中ES Modules的发展以及使用！
---
---
*编者注：这篇文章来自`John-David Dalton`，微软Edge团队的项目经理以及Lodash库的创始人，分享一个将ECMAScript模块带到Node新社区项目的消息。*

我很高兴宣布`@std esm`（standard / esm）的发布，这是一个可选且符合规范的ECMAScript（ES）模块加载器，能以接近内置模块的速度实现Node和ES Modules格式转换。这个快速、小型、零依赖的包可以在Node4+中启动ES Modules！

![img1](https://i.loli.net/2018/07/24/5b5741464f585.gif)

在Node中运行@std/esm

## 两种模块格式的历史

随着ESM在浏览器的逐步运用，人们也希望Node未来也能支持ESM。但与浏览器不同的是，它是解析器解析的，没有预定的规范。所以在Node中支持ESM需要做的事情比较多。Node模块的规范`CommonJS`（CJS）是Node受欢迎的的重要原因，但CJS也使Node支持ESM变得复杂化。我们来看看两个模块语法的例子。

CJS:
```javascript
const a = require("./a")
module.exports = { a, b: 2 }
```
esm:
```javascript
import a from "./a"
export default { a, b: 2 }
```
注：如果想要更深入的比较，请参照`Nicolás Bevacqua`的文章
`https://ponyfoo.com/articles/es6-modules-in-depth#the-es6-module-system`

由于CJS和ESM不兼容，因此必须加以区分。经过讨论，Node已经确定使用".mjs"(modular JavaScript)扩展来解析模块文件。Node具有通过文件扩展处理资源的历史。例如你需要.json文件，Node可以使用JSON.parse来处理和加载。

在2018年4月左右，Node v10里的ESM支持计划落地。这让开发者、尤其是包的作者陷入非常困难的境地。他们有如下选择：

- 全部使用ESM，旧版本不在维护
- 等到2020年1月1日，Node8不再支持，然后再使用
- 扩大包的大小同时支持CJS和ESM，确保两种用户都能使用

以上的选择好像都没有那么完美，生态系统的建立需要跨越一些CJS和ESM的不同。

![img2](https://i.loli.net/2018/07/24/5b5741461f6d7.png)

## 集成桥接
`@std/esm`是填补模块间差异的包。由于Node现在已经支持了大多数ES2015的功能，`@std/esm`可以自由选择是否启用ESM。
这个模块加载器有以下特点：

- 不污染堆栈
- 可以和`Babel`、`Webpack`一起使用
- 和`babel-register`一起使用也表现不错(*使用.babelrc "modules":false*)
- 只处理明确依赖@std/esmas的文件，包括dev依赖关系和对等关系
- 支持版本控制（*即包“A”依赖一个版本的`@std/esm`、包“B”依赖另一个版本*）

与CJS的ESM解决方案不同的是，`@std/esm`在运行时根据需要执行最少的源码转换、处理和缓存。 运行时处理文件有很多优点。

- 只处理使用的代码
- 所有Node版本的执行代码都是相同的
- 用户可配置其功能(*例如模块A使用默认配置使用模块C，而模块B使用cjs compat规则使用模块C*)
- 遵循特定规范（*即`@std/esm`能通过环境变量、错误代码、路径协议以及分解执行Node中的ESM规则*）

## 标准的特点
默认值很重要。 `@std/esm`尽可能遵循Node的内置规范，这就意味着，默认情况下，ESM需要使用.mjs扩展。
`@std/esm`开箱即用，无需额外配置，并且支持：

- `import/export`
- `动态import()`
- `文件URI格式`
- `实时绑定`
- `将.mjs文件作为ESM加载`

## 其他功能
开发者希望一切都可配置。所以@std/esm允许在package.json中使用"@std/esm"字段来解锁其他功能。选项包括：

- 启用明确的模块支持（即文件至少需要使用import、export或者ues module代码才能被视作ESM）
- 支持EJS模块的`named exports`
- `await`在主模块中具有最高级
- 加载gzip模块

## 性能
在我开始之前，让我明确下面的部分:
*It’s still super early, mileage may vary, and results may be hand wavey!*

在`PR #14369`中已经通过Node 9编译测试完毕了，Node能支持内置ESM。 我测量了加载`loadsh-es`的643个模块并且转换为.mjs所花费的时间，提供一个加载的基准。请记住，`@std/esm`缓存能优化不修改的文件。这意味着理性情况下生产环境中只能有一个非缓存的负载。

- 加载每个CJS模块大约需要0.28毫秒
- 加载每个内置ESM模块大约需要0.51毫秒
- `@std/esm`第一次无缓存时运行每个模块大约1.6毫秒
- `@std/esm`第二次有缓存时运行每个模块大约0.54毫秒

初始化结果看起来还不错，很有希望，`@std/esm`使用缓存加载能接近内置模块的性能！我能肯定的是在你的帮助下，解析和运行时的性能将会不断提高。

## 入门
1. 在你的APP或者包目录中运行`npm i --save @std/esm` 
2. 在导入ES modules之前调用`require("@std/esm")`

```javascript
// index.js:
require("@std/esm")
module.exports = require("./main.mjs").default

// 对于子模块的包作者:

// 使用"foo"模块只需要
"@std/esm". require("foo")
// 子模块就能使用了
const bar = require("foo/bar").default
```

使用`-r`选项加载`@std/es`m能在`Node CLI`中启用ESM：

```javascript
node -r @std/esm file.mjs
```

输入如下命令时，`@std/esm`在Node中启用ESM：

```javascript
$ node
> require("@std/esm")
@std/esm enabled
> import p from "path"
undefined
> p.join("hello", "world")
'hello/world'
```

## Meteor的力量
没有`Ben Newman`，`@std/esm`加载器就不会存在，他是`Reify`编译器的作者，`Reify`就是来自`@std/esm`，他证明了在`Meteor`的生成环境下实现加载器，自从2016年5月以来，已经涌现了成千上万的Metor开发的应用。

## 优点（All green thumbs ）
即使`@std/esm`才刚刚发布，它已经在很多相关项目上有了积极的影响：

- 修复Acorn严格模式下的编译检测和规范解析器的API
- 改进`Babel`和`Acorn`插件支持动态import（Acorn插件的动态import被用于webpack的代码分割）
- 改进ChakraCore中属性迭代顺序
- 改善Reify的解析加载时间以及代码规范
- 灌输快速顶级解析器的概念
- 带动 `export * as ns from "mod"` 和 `export default from "mod"`这两种写法

## 下一步（What’s next）
和许多开发者一样，我也打算使用ES Modules。我打算在Lodash v5中使用`@std esm`，不仅可以转换到ESM，还支持gzip模块，这样能大大减少包的大小。

`@std/esm`加载器在GitHub上已经可以用了。我希望别人像我一样兴奋和充满激情。ES Modules来了！这只是开始。接下来是由你决定。我期待看到你使用它。

## 最后的一点想法（Final Thought）
虽然这不是微软的发行版，但我们为微软基础JavaScript框架、库以及程序代码的核心贡献者感到越来越自豪。贡献者如 `Moment.js`的`Maggie Pint`，`ReactiveX`的`Matthew Podwysocki`，`PouchDB`的`Nolan Lawson`，`Modernizr`的`Patrick Kettner`，`Aurelia`的`Rob Eisenberg`，`Webpack`的`Sean Larkin`，以及`Ember`的`Tom Dale`等等，他们正通过生态系统的标准来塑造JavaScript和整个网络的未来。我很高兴在Microsoft Edge博客上分享这个消息，与社区的朋友分享我们的热忱！
