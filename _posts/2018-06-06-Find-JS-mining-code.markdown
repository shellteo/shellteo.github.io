---
layout: post
title:  "由一个bug找到JS挖矿代码"
date:   2018-06-16
categories: JS
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/miner-block.jpg"
description: 通过使用一个开源框架，发现了bug，在找bug的过程中发现了js挖矿代码！
---
---
由于工作需要，需要前端画流程图，找到一个开源的框架**gooFlow**（下图就是这个开源框架），它可以编辑流程图，提供节点和线以及一些辅助功能。仔细看一下这是一个国人写框架，文档也是中文，用起来也很顺手。

![Image1.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/Image1.png)

但是使用这个框架的时候发现一个很奇怪的bug，描述起来就是：**我在父页面的a标签点击跳转到流程图页面，如果a标签的target是_top，页面就不会报错，但是如果target的值为_blank，页面第一次打开不会报错，只有在刷新的情况下页面才会报错。**报错信息如下

![Image2.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/Image2.png)

其实从这个信息推断不出来什么具体信息，只知道jQuery这个对象应该不存在，所以导致程序出错，仔细检查我的页面代码的引入，发现jQuery都有引入，都没问题，结合这个bug出现的时机，只在新开页面且刷新过一次的情况下才会报错，所以可以推断出这个gooFlow框架应该是引用了父页面的某个对象，然后在刷新一次页面的情况下，父页面的这个对象为空才会导致报错。

接着我就开始搜索gooFlow的**window.opener**和**window.parent**的引用，**window.opener返回的是创建该窗口的 Window 对象的引用，window.parent返回的是返回当前窗口的父窗口**。但是都没有找到导致bug的源头。

束手无策的时候，尝试使用最原始的方法，**删除父页面上的js引用**，看流程图页面抛出什么错误，当我把父页面的jQuery删除的时候，页面报了不一样的错误（下图）。

![Image3.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/Image3.png)

找到对应方法处的代码，如下图，是使用**eval**执行一串字符串，很不好调试，只好把这一串代码拷贝出来执行一下看执行的是什么代码？

![Image4.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/Image4.png)

正当我纳闷为啥需要对框架里面的**代码加密**的时候，我找到了eval执行的字符串，格式化之后如下，保存的原因找到了，**_opener.$**的使用就是对**打开这个页面的jQuery的引用**，所以删除父页面jQuery报错信息会不一致。代码直接贴上来会被window安全中心当作病毒block掉，这里放一下图片。

![img](http://zxblog.oss-cn-hangzhou.aliyuncs.com/20190925/code.png)

最后在网上找到了，这是**门罗币的挖矿代码**。具体可以看看下面这篇文章：

> [JS挖矿 - 门罗币是如何利用网页挖矿的？](http://liujinkai.com/2017/10/28/js-miner/)

现在很多开源的框架、插件、库，使用起来都要小心，一不小心就中了别人的圈套，挖这种事是牺牲用户体验的基础上进行的，可以看到cpu使用飙升，电脑发烫，所以如果你为了你的网站优化用户体验的时候，发现怎么优化都无济于事，记得找一找你引用代里面有哪些隐藏的圈套。


最后推荐一下chrome阻止挖矿的插件**minerBlock**

![Image5.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article23/Image5.png)

最后，这篇文章投稿了**前端早读课**：[链接](https://mp.weixin.qq.com/s/fLGaVR-F8hFtZmHMFaNB4A)

最后插一句，为了避免恶意链接网站篡改，a标签跳转写法
```html
<a href="https://xxx" target="_blank" rel="noopener noreferrer">xxx</a>
```