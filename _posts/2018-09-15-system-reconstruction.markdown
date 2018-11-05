---
layout: post
title:  "工单审批系统重构文档"
date:   2018-09-15
categories: js
author: 张翔
location: ShangHai, China
cover: ""
description: 老系统存在的问题
---
---
## 老系统存在的问题

页面设计问题：风格不统一，具体来说是字体、字体颜色、背景颜色、动画效果不统一，很多冗余设计（弹出框，收起侧边栏等），页面布局问题很多，兼容性差。

代码问题： 表现和行为不分离，导致代码可读性和维护性很差，做这个Web系统的程序员是根据小程序版本代码来写的，很多在小程序员迂回实现的功能在Web端都很容易实现，所以代码很冗余，函数体非常长，函数命名不规范，代码组织存在很大的问题，整体代码逻辑非常混乱，Web端包含小程序上各种无用的判断和循环，浪费性能和造成不必要的bug，不合理的实现方式和冗余的代码导致的性能问题很难在原版本上进行优化。所以无论是去修改bug还是在原有基础上添加新功能，都是费力不讨好（开发效率低下而且被使用人员吐槽）。

## 新系统重构优化方向

布局、交互、代码组织、css管理、Vue状态控制，抽离特殊逻辑。总得来说就是梳理代码逻辑，然后推倒重写。系统从刀耕火种到自动化还有很长一段路要走！
技术栈使用vue+vuex+vue-router+axios+element-ui。

![shellteo](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/ticket/newticket.png)

## 1.布局

老系统布局全部自己实现，但是由于编码人员水平很有限，布局这一块轮子造得很差，页面莫名抖动，页面元素莫名消失，各种兼容问题以及莫名出现的横向滚动条都是由于布局的问题，我一块布局直接使用element-ui的布局，结构代码如下。

```html
<el-container>
  <el-header>顶部导航栏</el-header>
  <el-container>
    <el-aside>侧边栏</el-aside>
    <el-container>
      <el-main>主要页面</el-main>
      <el-footer>底部栏</el-footer>
    </el-container>
  </el-container>
</el-container>
```
侧边导航栏需要实现双层导航栏收起打开的效果，效果图如下
这一块使用的css动画自己实现的，侧边导航栏对应页面的面包屑导航栏。

## 2.交互和样式统一
举个例子：老系统前端开发人员感觉废了很大力气搞了一个弹窗框，效果非常炫，扑面而来的那种，但是和整个系统完全不搭，这就是时间都浪费在了不必要造的轮子上。
老系统连字体大小，颜色等都不统一，主要看当时写这个系统的程序员心情和审美，所以做出来的东西给人感觉很不专业！
新系统字体、颜色、动画也是根据element-ui来的，如下：

## 3.代码组织
我觉得代码组织就是一个项目的灵魂，如果没有一个好的目录组织，到时候写代码就是怎么写怎么别扭，也会影响到你写代码的思路。
我对这个系统的理解核心是配置控件的解析，所以说这一块控制粒度一定要细，在代码中的体现就应该是每一个控件单独对应一个文件，这样便于控制，也易于扩展，代码组织结构清晰，可读性好！

![shellteo](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/ticket/file.png)

## 4.CSS结构
我只写了一个base.css用于放脚手架通用css代码
这一块我没做什么特殊处理，后面可以按下面内容进行优化。
大型项目的css结构如下：

**base**：在这里，把所有的脚手架代码都放进去。通过脚手架，我指的是每次启动一个新项目时要写的所有CSS代码。例如：排版规则、动画、公用（公用类，我指的是margin-right-large，text-center，……之类的类）。

**components**：这里的名称是明确的。此文件夹包含用于构建页面的所有组件，如按钮，表单，swipers，弹出窗口等。

**layout**：用于布局页面的不同部分。也就是说，页眉，页脚，导航，自己的网格等等。

**pages**：你有时可能会有一个页面会具有一些自己特定的样式。将它们与那些通用的样式分开，然后将它们放在pages文件夹中。

**themes**：如果你的应用有不同的主题（黑暗模式，管理员模式等），请将它们放在此文件夹中。

**abstracts**：将所有函数与变量和mixin一起放在这里。

**vendors**：现在有什么项目是没有依赖外部库的？在vendor文件夹中放入所有不依赖于你的文件。你可能希望在此处添加Font Awesome文件，Bootstrap和类似的东西。


## 5.特殊控件优化
**1.老系统项目图片查看**特别麻烦，点击链接然后浏览器打开图片，完全没有预览功能。新系统这一块的优化就是使用v-viewer插件进行图片预览，v-viewer分为指令式调用和组件式调用，指令式调用模式有点问题，有时候预览图片会弹出很多预览层，这一点我已经和这个插件作者说了，他说下一版本会优化，所以期待吧。
11月2号更新，bug已经修复！
**2.上传附件**，老系统上传附件代码各自为营，好多地方使用都不一样，这些代码都是可复用的。分离出来的上传附件控件包括上传事件、上传校验事件、删除附件事件，使用vue自定义事件，组件去修改父类的data中的数据。（vue2拒绝子组件直接修改prop中的引用对象，所以可以用自定义事件来实现）

```javascript
<UploadFile @setFileList="setUploadList"></UploadFile>

import UploadFile from './UploadFile'

data() {
  return {
    UploadList: [],
  }
},
setUploadList(payload) {
  if (payload.type === 'add') {
    this.UploadList.push(payload.file)
  } else if (payload.type === 'delete') {
    this.UploadList.splice(payload.index, 1)
  }
}
```
**3.列表优化**

老系统自己实现的列表很难看，因为列很多自适应小屏幕之后列都挤在了一起，新系统使用element-ui的列表，一些重要的列固定在左右两边，如果小屏上表格超出，不完全自适应，显示横向滚动条，但是有一个问题，就是使用者每次看超出的列都需要去最下面拉滚动条，体验不是很好，这里做了一个优化就是表格内部，鼠标滚轮滚动，横向滚动条也滚动。代码如下：
```javascript
//监听列表横向滚动
tableHorizontalScroll () {
	let scrollTable = document.querySelector('.el-table__body-wrapper');
	let isFF = /FireFox/i.test(navigator.userAgent);
	let MouseWheelHandler = (e) => {
		e.preventDefault();
		this.tableScrollLeft = scrollTable.scrollLeft
		if (isFF) {
			scrollTable.scrollLeft += e.detail * 10;
		} else {
			scrollTable.scrollLeft += -e.wheelDelta / 6;
		}
		return false;
	};
	if (!isFF) {
		scrollTable.addEventListener('mousewheel', MouseWheelHandler, false)
	} else {
		scrollTable.addEventListener('DOMMouseScroll', MouseWheelHandler, false)
	}
},
```
## 6.数据优化
**1.页面loading优化**
判断页面全部加载完毕

**2.打印页面数据格式优化**

**3.select下拉框设计和优化**

## 7.特殊逻辑抽离
工单和审批类型特别多，有时候代码里面牵扯到写死的逻辑判断，老系统是直接写在每一个函数体内部，但是如果写死的部分配置发生变化，前端如何最快响应变化进行修改呢。如果每个函数体都有特殊写死的逻辑判断，修改起来就会很麻烦，后期功能越来越复杂，类型越来越多之后，代码就会变得不可控。所以梳理代码逻辑抽离其中特殊逻辑单独作为函数是重构中重要一环。

## 8.测试
如果没有专门的bug记录工具，在老系统测试完之后就需要一个bug文档记录，而且在后期上的每一个新功能都需要记录在案，主要是方便重构之后，可以首先测试以前上的新功能是否都包含在新系统中，然后看一下以前出现的bug是否在新系统中存在。简化了测试，也提高了开发效率。

## 9.final thought
系统重构优化完了之后还有很多事情需要去做
1. css代码规范性和css代码模块化

2. 页面自适应手机端

3. 交互修改，键盘操作等

4. 前端自动化构建部署（FIS3了解一下）

5. 优化vendor.js的大小，vue和vuex直接cdn引入，常用的UI框架也cdn引入，全局配置

```javascript
// 在webpack.base.conf.js添加
externals: {
    'vue': 'Vue',
    'vuex': 'Vuex',
    'vue-router': 'VueRouter',
    'element-ui': 'ELEMENT'
}
//.eslintrc.js添加，防止报错
globals: {
	'Vue': true,
	'Vuex': true,
	'VueRouter': true,
	'ELEMENT': true
}
// 页面中使用
const { Message } = ELEMENT
export default new VueRouter({})
```

