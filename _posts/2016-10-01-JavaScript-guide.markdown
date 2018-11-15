---
layout: post
title:  "《JavaScript权威指南》读书笔记"
date:   2016-10-01
categories: js
author: 张翔
location: NanChang, China
cover: ""
description: 《JavaScript权威指南》第二部分的读书笔记
---
---
《JavaScript权威指南》第二部分的读书笔记
包含“第13章 web浏览器中的javascript对象”、“第14章 window对象”、“第15章 脚本化文档”、“第16章 脚本化css”、“第17章 事件处理”、“第18章 脚本化http”、“第19章 jquery类库”、“第19章 jquery类库”。


# 13章：web浏览器中的javascript对象

![00.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/00.png)

```javascript
//getElementsByClassName:获得一个documentNode数组
var now = new Date(); // Get current time
elt.innerHTML = now.toLocaleTimeString(); //获得当前时间
```

![01.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/01.png)

**当html解析器遇到<script>标签时，它必须先执行脚本，然后再恢复文档的解析和渲染。**

![02.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/02.png)

# 14章：window对象

![03.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/03.png)

![04.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/04.png)

# 15章：脚本化文档

### 节点树和元素树的区别

**节点树**：遍历DOM树中的节点，包含元素节点之外的其他节点，如注释节点、文字节点（包括之间的空格）等
属性有：**parentNode**，**childNodes**，**firstChild**，**lastChild**，**nextSibling**，**previousSibling**

![05.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/05.png)

元素树：遍历DOM树中的元素（element）节点，而不包含元素节点之外的其他节点，如注释节点、文字节点（包括之间的空格） 等。属性有如下：

![06.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/06.png)

```html
<div id="container">    
	这里是文字节点。    
	<br/>    
	<!-- 这里是注释文字 -->    
	<div>这个是真正的节点。</div>    
	<mxh>自定义标签被认可吗？</mxh>
</div>
<script>
	window.onload  = function () {
	    var container = document.getElementById("container");
	    console.log(container.childNodes);
	    console.log(container.children);
	}
</script>
```
**结果如下：**
![07.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/07.png)

# 15.4.1 HTML属性作为Element的属性

![08.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/08.png)

#15.4.2 获取和设置非标准HTML属性

![09.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/09.png)

# 15.4.3 数据集属性

![10.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/10.png)

![11.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/11.png)

# 15.5.1 作为HTML的元素内容

![12.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/12.png)

# 15.5.2 作为纯文本的元素内容

![13.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/13.png)

# 15.6 创建、插入和删除节点

![14.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/14.png)

# 15.6.2 插入节点

![15.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/15.png)

**insertBefore用法实例**  

# 15.6.3 删除和替换节点

![16.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/16.png)

# 16章：脚本化CSS
设置元素的style属性，直接`e.style.property="";`
元素的Style属性代表了元素的内联样式。
获取元素的style属性，代码需要很强的css解析能力，不能直接获取`var p=e.style.property;`如下：

![17.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/17.png)

# 17章：事件处理
## 2.Window事件
![18.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/18.png)

```javascript
window.onbeforeunload = function(event) {
     return confirm("确定退出吗");
}
```

![19.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/19.png)

## addEventListener 和 removeEventListener

![20.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/20.png)

## attachEvent和detachEvent

![21.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/21.png)

## 17.3事件处理程序的调用

![22.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/22.png)

![23.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/23.png)

![24.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/24.png)

![25.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/25.png)

![26.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/26.png)

## 冒泡的例外情况

![27.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/27.png)

## 17.4文档加载事件

![28.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/28.png)

## 17.5鼠标事件

![29.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/29.png)

![30.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/30.png)

## 拖动函数

![31.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/31.png)

# 18章：脚本化http

![32.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/32.png)

## AJAX终止ajxa请求
xhr.abort();

# 20章：客户端存储

![33.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/33.png)

**其他浏览器兼容的写法如下**
![34.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/34.png)

## 20.1.3存储事件

![35.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/js-guide/35.png)