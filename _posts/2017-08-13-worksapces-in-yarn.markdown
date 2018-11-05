---
layout: post
title:  "yarn的WorkSpaces功能介绍"
date:   2017-08-13
categories: yarn
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article3/images.png"
description: 我的翻译计划第一篇，主要介绍多包管理的优化，以及Yarn在多包管理上的运用。
---
---
随着时间的增长，项目会变得越来越大，偶尔有的项目中的一部分在其他项目中会被用到。举个例子，`Jest`作为一款通用的测试工具，有很多包都是基于它的，其中包括`jest-snapshot`，而`snapguidist`和`chai-jest-snapshot`都有用到`jest-snapshot`。

## Monorepos
将项目分解为多个包的人知道一次修改多个包是非常困难的，为了使这个过程变得更加容易，一些大型项目采用`monrepo`方法或者多包存储，为了就是减少包中编写代码的负担。
JavaScript开发热暖每天使用的几个项目都是使用`monrepos`管理的，像`Babel`、`React`、`Jest`、`Vue`、`Angular`。
然而将项目分成几个文件夹有时候是不够的。测试、管理依赖和发布多个包很快会变得复杂，很多项目采用像`Lerna`这样的工具来降低复杂度。

## Lerna
Lerna能优化git和npm管理多包存储的工作流。Lerna内部使用`Yarn`和npm CLI来引导项目（即为每个包安装所有第三方依赖）。简单来说，对项目中的每个使用的包Lerna都是用yarn/npm进行安装，然后在引用的包之间创建软链接。
Lerna只是对包管理器进行了一层包装，所以Lerna无法有效的处理node_modules里面的内容

>* Lerna通过 yarn install 安装包，这会对重复引用的包多次安装，这样会使得项目变得过载，因为package.json里面的引用的包是相互独立的，不会批次共享依赖。所以经常使用的第三方包，都会重复安装，这会造成大量重复
* Lerna是先创建包，然后创建包之前的引用链接，这会造成的问题是会引起包管理器node_modules内的不一致，所以从包中运行yarn install可能会破坏Lerna管理的结构。

作为包的开发人员，这些问题使我们更应该直接从yarn中支持多包存储库，从yarn0.28开始，我们很高兴来分享支持这些存储的Workspaces功能。

## 介绍Yarn Workspaces
Yarn Workspaces允许用户在单个根package.json文件的子文件夹中从多个package.json文件中安装依赖。

通过防止Workspaces中依赖包的重复，使原生Workspaces到Yarn可以实现更快更轻松的依赖安装。Yarn还可以在依赖于彼此的Workspaces之间创建软链接，并确保所有目录的一致性和正确性。

### 安装Workspaces
如果要开始使用，用户必须在Yarn中运行如下命令来启用Workspaces
```javascript
yarn config set workspaces-experimental true
```

以上命令的作用是：它会在你的操作系统主文件夹的.yarnrc文件中添加workspaces-experimental true。Yarn Workspaces仍然会根据从社区收集来的反馈进行改进。
我们将以Jest为例来设置Yarn Workspaces的结构。事实上，它已经在一次PR（pull request）中被实现，Jest已经使用Yarn来引导它的依赖。
Jest的项目结构是开源JavaScript monorepo的典型代表。

>| jest/  
| ---- package.json  
| ---- packages/  
| -------- jest-matcher-utils/  
| ------------ package.json  
| -------- jest-diff/  
| ------------ package.json  
...

最外层的的`package.json`定义整个的项目的根依赖，其他目录的package.json文件就是Workspaces，Workspaces经常注册发布到npm上。虽然根package.json不应该作为一个包使用，它通常会包含与其他项目共享的代码或特定的业务代码，这就是我们将其标记为“私有”的原因。
以下的例子是一个简单的根`package.json`，它可以为项目启用Workspaces，并定义项目构建和测试环境所需的第三方依赖包。
```javascript
{
  "private": true,
  "name": "jest",
  "devDependencies": {
    "chalk": "^2.0.1"
  },
  "workspaces": [
    "packages/*"
  ]
}
```
为了简单，我们定义两个小的Workspaces依赖包

1. jest-matcher-utils Workspace:

```javascript
{
  "name": "jest-matcher-utils",
  "description": "...",
  "version": "20.0.3",
  "license": "...",
  "main": "...",
  "browser": "...",
  "dependencies": {
    "chalk": "^1.1.3",
    "pretty-format": "^20.0.3"
  }
}
```

jest-diff Workspace依赖于jest-matcher-utils：
```javascript
{
  "name": "jest-diff",
  "version": "20.0.3",
  "license": "...",
  "main": "...",
  "browser": "...",
  "dependencies": {
    "chalk": "^1.1.3",
    "diff": "^3.2.0",
    "jest-matcher-utils": "^20.0.3",
    "pretty-format": "^20.0.3"
  }
}
```
像Lerna这样的一层包装会首先为每个`package.json`运行`yarn install`，然后为相互依赖的包运行`yarn link`。
如果我们使用这种方法，我们会得到如下文件结构：

>| jest/  
| ---- node_modules/  
| -------- chalk/  
| ---- package.json  
| ---- packages/  
| -------- jest-matcher-utils/  
| ------------ node_modules/  
| ---------------- chalk/  
| ---------------- pretty-format/  
| ------------ package.json  
| -------- jest-diff/  
| ------------ node_modules/  
| ---------------- chalk/  
| ---------------- diff/  
| ---------------- jest-matcher-utils/  (symlink) -> ../jest-matcher-utils  
| ---------------- pretty-format/  
| ------------ package.json  
...

我们能看到，第三方依赖会存在冗余重复。
启用Workspaces后，Yarn能生成更优化的依赖关系结构，当你在项目任何地方运行`yarn install`，你会得到如下的node_modules。


>| jest/  
| ---- node_modules/  
| -------- chalk/  
| -------- diff/  
| -------- pretty-format/  
| -------- jest-matcher-utils/  (symlink) -> ../packages/jest-matcher-utils  
| ---- package.json  
| ---- packages/  
| -------- jest-matcher-utils/  
| ------------ node_modules/  
| ---------------- chalk/`  
| ------------ package.json  
| -------- jest-diff/  
| ------------ node_modules/  
| ---------------- chalk/  
| ------------ package.json  
...  

像`diff`和`pretty-format`这样的依赖包还有`jest-matcher-utils`的软链接被提升到根node_modules目录，使安装越来越快，安装包越来越小。然而`chalk`依赖包不能被移动到根部，因为根部已经依赖了不同版本且不兼容的`chalk`了。
上述的两种结构是兼容的，但是后一种结构优化了Node.js模块分辨率逻辑，而且引用的依赖仍然是正确的。
对于狂热的Lerna用户，可以通过`--hoist`来引导代码。
如果你在`jest-diff` Workspace中运行代码，它能够解析其他所有依赖。

>* require(‘chalk’)解析为./node_modules/chalk
* require(‘diff’)解析为../../node_modules/diff
* require(‘pretty-format’) 解析为../../node_modules/pretty-format
* require(‘jest-matcher-utils’)解析为../../node_modules/jest-matcher-utils，这是../packages/jest-matcher-utils的软链接


## 管理Workspaces中依赖
如果要修改Workspace中的依赖关系，只需在Workspace文件夹中运行相应的命令：
```javascript
$ cd packages/jest-matcher-utils/
$ yarn add left-pad
✨Done in 1.77s.
$ git status
modified: package.json
modified: ../../yarn.lock
```
需要注意的是，Workspaces中没有自己的yarn.lock文件，在根文件夹的yarn.lock包含所有Workspaces的依赖项。当您要更改Workspace中的依赖关系时，根文件夹的yarn.lock将与Workspace的package.json一起更改。

## 集成Lerna
Yarn Workspaces使Lerna过时了吗？
一点也不会过时，Yarn Workspaces能够轻松的和Lerna集成。
Lerna不仅仅能为一个项目提供引导，而且它有一个用户社区能够修改Lerna来满足他们的需求。
从Lerna 2.0.0开始，当你通过`-use-workspaces`标志运行Lerna的命令时，它将会使用Yarn来引导项目，并且还会使用`package.json/workspaces`字段来查找依赖，而不是使用`lerna.json/packages`。
下面是Lerna的Jest配置：
```javascript
{
  "npmClient": "yarn",
  "useWorkspaces": true
}
```

Jest依靠Yarn来引导项目，基于Lerna发布。

## 下一步？
Yarn Workspaces是包管理者在管理monrepos时可以做的第一步，因为它们成为更常见的代码共享解决方案。
同时我们也不想将所有可能的monorepo功能放在Yarn上。 我们想要保持Yarn的精简，这意味着像Lerna这样的项目将继续和Yarn协同合作。
我们的下一个目标是确定Yarn1.0，这是为了总结我们过去一年在Yarn上所做的工作，并认识到Yarn的可靠性如何。 我们也会分享接下来我们想要构建Yarn的想法，
敬请关注。

