---
layout: post
title:  "《WebKit技术内幕》JavaScripty引擎-读书笔记"
date:   2018-10-24
categories: js
author: 张翔
location: ShangHai, China
cover: ""
description: 这是第九章-JavaScript引擎部分的读书笔记
---
---

# 9.1.1 JavaScript语言

![61.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/61.png)

**JavascriptCore引擎和V8引擎已经优化了效率，推动Javascript运行速度提高的另一大利器JIT（Just-In-Time）。**
![62.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/62.png)

### JavaScript作用域链和闭包
![63.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/63.png)

**如果“add”函数上下文没有包含该变量，那么执行环境会不停向上查找，知道找遍全局上下文为止。**

![64.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/64.png)

# 9.1.2 JavaScript引擎
### 先看C/C++语言
![65.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/65.png)

### 然后看 Python等脚本语言
![66.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/66.png)

### 然后看Java
![67.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/67.png)

### 最后回到JavaScript
![68.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/68.png)

# 9.1.3 JavaScript引擎和渲染引擎

![69.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/69.png)

# 9.1.4 V8引擎-代码结构

![70.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/70.png)

# 9.2.2.2 V8工作过程

![71.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/71.png)

**工作过程：优点很明显，缺点有二。**
![72.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/72.png)

![73.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/73.png)

# 9.2.2.3 优化回滚（Deoptimization）

![74.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/74.png)

# 9.2.2.4 隐藏类和内嵌缓存

![75.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/75.png)

# 9.2.2.5 V8-内存管理

一系列小内存的划分，Zone类，一次申请，同时释放
堆，同很多虚拟机一样，V8将堆分成三个部分，第一部分是年轻分代，第二个是年老分代，第三个是大对象

![76.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/76.png)

# 9.3 JavaScriptCore引擎
JavaScriptCore和V8引擎不同的是，JavaScriptCore引擎不是从抽象语法树生成本地代码，而是生成平台无关的字节码。

![77.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/77.png)

![78.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/78.png)

# 9.4.1 实践-高效的JavaScript代码-编程方式

![79.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/79.png)

# 9.4.2 实践-高效的JavaScript代码-例子

![80.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/80.png)

![81.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/81.png)

### setTimeout和setInterval的不足

![82.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/82.png)

### requestAnimationFrame如何解决不足之处的

![83.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/webkit/83.png)