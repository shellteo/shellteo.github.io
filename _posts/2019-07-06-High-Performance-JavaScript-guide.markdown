---
layout: post
title:  "《高性能Javascript》读书笔记"
date:   2019-07-06
categories: js
author: 张翔
location: ShangHai, China
cover: ""
description: 之前有看过这本书，出版于2010年11月，全书多处讲到对于IE的优化，浏览器更迭很快，现在看来有点过时了，但是很多东西还是写得很好的。这里再细读一下，作笔记如下。
---
---

之前有看过这本书，出版于2010年11月，全书多处讲到对于IE的优化，浏览器更迭很快，现在看来有点过时了，但是很多东西还是写得很好的。这里再细读一下，作笔记如下。

# 第一章 Loading and Execution 加载和运行

## Script Positioning 脚本位置   

此代码展示了所推荐的`<script>`标签在 HTML 文件中的位置。尽管脚本下载之间互相阻塞，但页面已经 下载完成并且显示在用户面前了，进入页面的速度不会显得太慢。这正是“Yahoo! 优越性能小组”关于 JavaScript 的第一条定律：将脚本放在底部。

## Grouping Scripts 成组脚本

由于每个`<script>`标签下载时阻塞页面解析过程，所以限制页面的`<script>`总数也可以改善性能。[yahoo YUI打包工具](http://yui.yahooapis.com/combo?2.7.0/build/yahoo/yahoo-min.js&2.7.0/build/event/event-min.js)

## Nonblocking Scripts  非阻塞脚本

非阻塞脚本的秘密在于，等页面完成加载之后，再加载 JavaScript 源码。从技术角度讲，这意味着在 window 的 load 事件发出之后开始下载代码。有几种方法可以实现这种效果。

### Deferred Scripts  延期脚本
```javascript
<script type="text/javascript" src="file1.js" defer></script>
```
一个带有 defer 属性的`<script>`标签可以放置在文档的任何位置。对应的 JavaScript 文件将在`<script>`被解析 时启动下载，但代码不会被执行，直到 DOM 加载完成（在 onload 事件句柄被调用之前）。当一个 defer 的 JavaScript 文件被下载时，它不会阻塞浏览器的其他处理过程，所以这些文件可以与页面的其他资源一 起并行下载。

defer和async的区别： [https://segmentfault.com/q/1010000000640869](https://segmentfault.com/q/1010000000640869)

### Dynamic Script Elements  动态脚本元素
文档对象模型（DOM）允许你使用 JavaScript 动态创建 HTML 的几乎全部文档内容。其根本在于，`<script>` 元素与页面其他元素没有什么不同：引用变量可以通过 DOM 进行检索，可以从文档中移动、删除，也可 以被创建。一个新的`<script>`元素可以非常容易地通过标准 DOM 函数创建：

```javascript
var script = document.createElement ("script"); 
script.type = "text/javascript"; 
script.src = "file1.js"; 
document.getElementsByTagName_r("head")[0].appendChild(script);

```

Firefox, Opera, Chorme 和 Safari 3+会在`<script>`节点接收完成之后发出一个 load 事件。你可以监听这一 事件，以得到脚本准备好的通知：

```javascript
var script = document.createElement ("script") 
script.type = "text/javascript"; 
//Firefox, Opera, Chrome, Safari 3+ 
script.onload = function(){   
    alert("Script loaded!"); 
}; 
script.src = "file1.js"; 
document.getElementsByTagName_r("head")[0].appendChild(script);

```
Internet Explorer 支持另一种实现方式，它发出一个 readystatechange 事件。`<script>`元素有一个 readyState 属性，它的值随着下载外部文件的过程而改变。readyState 有五种取值： “uninitialized”默认状态、“loading”下载开始、“loaded”下载完成、“interactive”下载完成但尚不可用、“complete”所有数据已经准备好

```javascript
function loadScript(url, callback) {
  var script = document.createElement("script")
  script.type = "text/javascript";
  if (script.readyState) { //IE
    script.onreadystatechange = function () {
      if (script.readyState == "loaded" || script.readyState == "complete") {
        script.onreadystatechange = null;
        callback();
      }
    };
  } else { //Others
    script.onload = function () {
      callback();
    };
  }
  script.src = url;
  document.getElementsByTagName_r("head")[0].appendChild(script);
}    
```

## XMLHttpRequest Script Injection XHR 脚本注入

此方法最主要的限制是： JavaScript 文件必须与页面放置在同一个域内，不能从 CDNs 下载。

## Recommended Nonblocking Pattern 推荐的非阻塞模式



# 第二章 Data Access 数据访问

## Managing Scope 管理作用域

### Scope Chains and Identifier Resolution 作用域链和标识符解析

### Identifier Resolution Performance 标识符识别性能

在运行期上下文的作用域链中，一个标识符所处的位置越深，它的读写速度就越慢。
请记住，全局变量总是处于运行期上下文作用域链的最后一个位置，所以总是最远才能触及的。
通过以上信息，在没有优化 JavaScript 引擎的浏览器中，最好尽可能使用局部变量。一个好的经验法则是：**用局部变量存储本地范围之外的变量值，如果它们在函数中的使用多于一次**。

### Scope Chain Augmentation 改变作用域链

在 JavaScript 中不只是 with 表达式人为地改变运行期上下文的作用域链， try-catch 表达式的 catch 子句具有相同效果。当 try 块发生错误时，程序流程自动转入 catch 块，并将异常对象推入作用域链前端的一个可变对象中。在 catch 块中，函数的所有局部变量现在被放在第二个作用域链对象中。

### Dynamic Scopes 动态作用域

### Closures, Scope, and Memory 闭包，作用域，和内存

闭包是 JavaScript 最强大的一个方面，它允许函数访问局部范围之外的数据。

由于闭包的[[Scope]]属性包含与运行期上下文作用域链相同的对象引用，会产生副作用。通常，一个函数的激活对象与运行期上下文一同销毁。当涉及闭包时，激活对象就无法销毁了，因为引用仍然存在于闭包的[[Scope]]属性中。这意味着脚本中的闭包与非闭包函数相比，需要更多内存开销。**造成内存泄露**。

### Object Members 对象成员
诸如文档对象模型（DOM）和浏览器对象模型（BOM）之中的对象。
### Prototypes 原形
### Prototype Chains 原形链
深入原形链越深，搜索的速度就会越慢。
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/1.png)

### Nested Members 嵌套成员
### Caching Object Member Values 缓存对象成员的值

由于所有这些性能问题与对象成员有关，所以如果可能的话请避免使用它们。更确切地说，你应当小心地，只在必要情况下使用对象成员。例如，没有理由在一个函数中多次读取同一个对象成员的值：

一般来说，如果在同一个函数中你要多次读取同一个对象属性，最好将它存入一个局部变量。以局部变量替代属性，避免多余的属性查找带来性能开销。在处理嵌套对象成员时这点特别重要，它们会对运行速度产生难以置信的影响。

```javascript
function hasEitherClass(element, className1, className2){
    return element.className == className1 || element.className == className2;
}
```
```javascript
function hasEitherClass(element, className1, className2){
    var currentClassName = element.className;
    return currentClassName == className1 || currentClassName == className2;
}
```


## Summary 总结

1. 在 JavaScript 中，数据存储位置可以对代码整体性能产生重要影响。有四种数据访问类型：直接量，变量，数组项，对象成员。它们有不同的性能考虑。

2. 直接量和局部变量访问速度非常快，数组项和对象成员需要更长时间。

3. 局部变量比域外变量快，因为它位于作用域链的第一个对象中。变量在作用域链中的位置越深，访问所需的时间就越长。全局变量总是最慢的，因为它们总是位于作用域链的最后一环。

4. 避免使用 with 表达式，因为它改变了运行期上下文的作用域链。而且应当小心对待 try-catch 表达式的 catch子句，因为它具有同样效果。

5. 嵌套对象成员会造成重大性能影响，尽量少用。

6. 一个属性或方法在原形链中的位置越深，访问它的速度就越慢。

7. 一般来说，你可以通过这种方法提高 JavaScript 代码的性能：将经常使用的对象成员，数组项，和域外变量存入局部变量中。然后，访问局部变量的速度会快于那些原始变量。


# 第三章 DOM Scripting DOM 编程

## DOM in the Browser World 浏览器世界中的 DOM

浏览器通常要求 DOM 实现和 JavaScript 实现保持相互独立。例如，在 Internet Explorer 中，被称为 JScript的 JavaScript 实现位于库文件 jscript.dll 中，而 DOM 实现位于另一个库 mshtml.dll（内部代号 Trident）。这种分离技术允许其他技术和语言，如 VBScript，受益于 Trident 所提供的 DOM 功能和渲染功能。 Safari使用 WebKit 的 WebCore 处理 DOM 和渲染，具有一个分离的 JavaScriptCore 引擎（最新版本中的绰号是SquirrelFish）。 Google Chrome 也使用 WebKit 的 WebCore 库渲染页面，但实现了自己的 JavaScript 引擎V8。在 Firefox 中， JavaScript 实现采用 Spider-Monkey（最新版中称作 TraceMonkey），与其 Gecko 渲染引擎相分离。

## Inherently Slow 天生就慢

## DOM Access and Modification DOM 访问和修改

这些结果清楚地表明，你访问 DOM 越多，代码的执行速度就越慢。因此，一般经验法则是：轻轻地触摸 DOM，并尽量保持在 ECMAScript 范围内。

## innerHTML Versus DOM methods innerHTML 与 DOM 方法比较
更新页面时，使用虽不标准却被良好支持的 innerHTML 属性更好呢，还是使用纯 DOM 方法，如 document.createElement ()更好呢？如果不考虑标准问题，它们的性能如何？答案是：性能差别不大，但是，在所有浏览器中， innerHTML 速度更快一些，除了最新的基于 WebKit 的浏览器（Chrome 和 Safari）。

例子： 使用 innerHTML 和 DOM 方法创建一个 1000 行的表

结果： 在 IE6 中， innerHTML 比对手快三倍，但在最新的基于 WebKit 的浏览器中慢于对手

## Cloning Nodes 节点克隆
## HTML Collections HTML 集合
```javascript
document.getElementsByName()
document.getElementsByClassName()
document.getElementsByTagName_r()
document.images //页面中所有的<img>元素
document.links //所有的<a>元素
document.forms //所有表单
```

这些方法和属性返回 HTMLCollection 对象，是一种类似数组的列表。它们不是数组（因为它们没有诸如 push()或 slice()之类的方法），但是提供了一个 length 属性，和数组一样你可以使用索引访问列表中的元素。

类数组转数组：[ https://segmentfault.com/a/1190000010979127](https://segmentfault.com/a/1190000010979127)
```javascript
Array.prototype.slice.call(ArrayLike);
Array.from(arr)
```

## Expensive collections 昂贵的集合
不建议用数组的 length 属性做循环判断条件。访问集合的 length 比数组的length 还要慢，因为它意味着每次都要重新运行查询过程。

## Walking the DOM DOM 漫谈
### Crawling the DOM 抓取 DOM
你经常需要从一个 DOM 元素开始，操作周围的元素，或者递归迭代所有的子节点。你可以使用 childNode集合或者使用 nextSibling 获得每个元素的兄弟节点。

```javascript
  function testNextSibling() {
    var el = document.getElementById('mydiv'),
        ch = el.firstChild,
        name = '';
    do {
      name = ch.nodeName;
    } while (ch = ch.nextSibling);
    return name;
  }
```

在老的 IE 中性能严苛的使用条件下，用 nextSibling 抓取 DOM 是首选方法。在其他情况下，主要看个人和团队偏好。

### Element nodes 元素节点

DOM 属性诸如 childNode， firstChild，和 nextSibling 不区分元素节点和其他类型节点，如注释节点和文本节点（这两个标签之间往往只是一些空格）。在许多情况下，只有元素节点需要被访问，所以在循环中，似乎应当对节点返回类型进行检查，过滤出非元素节点。这些检查和过滤都是不必要的 DOM 操作。

许多现代浏览器提供了 API 函数只返回元素节点。如果可用最好利用起来，因为它们比你自己在JavaScript 中写的过滤方法要快。表 3-1 列出这些便利的 DOM 属性。
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/2.png)

### The Selectors API 选择器 API

**querySelectorAll()的使用**

当你需要联合查询时，使用 querySelectorAll()更加便利。例如，如果页面中有些 div 元素的 class 名称是"warning"，另一些 class 名是"notice"，你可以用 querySelectorAll()一次性获得这两类节点。

```javascript
var errs = document.querySelectorAll('div.warning, div.notice');
```
你还可以从另一个函数 querySelector()获益，这个便利的函数只返回符合查询条件的第一个节点。

## Repaints and Reflows 重绘和重排版（回流和重绘）
渲染树中为每个需要显示的 DOM 树节点存放至少一个节点（隐藏 DOM 元素在渲染树中没有对应节点）。渲染树上的节点称为“框”或者“盒”，符合 CSS 模型的定义，将页面元素看作一个具有填充、边距、边框和位置的盒。一旦 DOM 树和渲染树构造完毕，浏览器就可以显示（绘制）页面上的元素了。

当 DOM 改变影响到元素的几何属性（宽和高） ——例如改变了边框宽度或在段落中添加文字，将发生一系列后续动作——浏览器需要重新计算元素的几何属性，而且其他元素的几何属性和位置也会因此改变受到影响。浏览器使渲染树上受到影响的部分失效，然后重构渲染树。这个过程被称作重排版。重排版完成时，浏览器在一个重绘进程中重新绘制屏幕上受影响的部分。
不是所有的 DOM 改变都会影响几何属性。例如，改变一个元素的背景颜色不会影响它的宽度或高度。在这种情况下，只需要重绘（不需要重排版），因为元素的布局没有改变。

### When Does a Reflow Happen?
添加或删除可见的 DOM 元素、 元素位置改变、 元素尺寸改变（因为边距，填充，边框宽度，宽度，高度等属性改变）、 内容改变，例如，文本改变或图片被另一个不同尺寸的所替代、 最初的页面渲染、 浏览器窗口改变尺寸



### Queuing and Flushing Render Tree Changes 查询并刷新渲染树改变

Because of the computation costs associated with each reflow, most browsers optimize the reflow process by queuing changes and performing them in batches. However, you may (often involuntarily) force the queue to be flushed and require that all scheduled changes be applied right away. Flushing the queue happens when you wantto retrieve layout information, which means using any of the following:

- offsetTop, offsetLeft, offsetWidth, offsetHeight
- scrollTop, scrollLeft, scrollWidth, scrollHeight
- clientTop, clientLeft, clientWidth, clientHeight
- getComputedStyle() (currentStyle in IE)（在 IE 中此函数称为 currentStyle）

### Minimizing Repaints and Reflows 最大限度地减少重绘和回流
Reflows and repaints can be expensive, and therefore a good strategy for responsive applications is to reduce their number. In order to minimize this number, you should combine multiple DOM and style changes into a batch and apply them once.
重排版和重绘代价昂贵，所以，提高程序响应速度一个好策略是减少此类操作发生的机会。为减少发生次数，你应该将多个 DOM 和风格改变合并到一个批次中一次性执行。

```javascript
var el = document.getElementById('mydiv');
el.style.borderLeft = '1px';
el.style.borderRight = '2px';
el.style.padding = '5px';
```

```javascript
// 一个达到同样效果而效率更高的方法是：将所有改变合并在一起执行，只修改 DOM 一次。可通过使用cssText 属性实现：
var el = document.getElementById('mydiv');
el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px;';
```

这个例子中的代码修改 cssText 属性，覆盖已存在的风格信息。如果你打算保持当前的风格，你可以将它附加在 cssText 字符串的后面。也可以修改class。

```javascript
el.style.cssText += '; border-left: 1px;';
```

### Batching DOM changes 批量修改 DOM
当你需要对 DOM 元素进行多次修改时，你可以通过以下步骤减少重绘和重排版的次数：

1. Take the element off of the document flow  从文档流中摘除该元素
2. Apply multiple changes  对其应用多重改变
3. Bring the element back to the document.  将元素带回文档中

此过程引发两次重排版——第一步引发一次，第三步引发一次。如果你忽略了这两个步骤，那么第二步中每次改变都将引发一次重排版。

There are three basic ways to modify the DOM off the document: document修改DOM有三种方法
1. Hide the element, apply changes, and show it again.  隐藏元素，进行修改，然后再显示。
```javascript
var ul = document.getElementById('mylist');
ul.style.display = 'none';
appendDataToElement(ul, data);
ul.style.display = 'block';

```
2. Use a document fragment to build a subtree outside of the live DOM and then copy it to the document. 使用文档片段，**推荐**

```javascript
var fragment = document.createDocumentFragment();
appendDataToElement(fragment, data);
document.getElementById('mylist').appendChild(fragment);
```

3. Copy the original element into an off-document node, modify the copy, and then replace the original element once you're done. 将原始元素复制到文档外节点，修改副本，然后在完成后替换原始元素。
```javascript
var old = document.getElementById('mylist');
var clone = old.cloneNode(true);
appendDataToElement(clone, data);
old.parentNode.replaceChild(clone, old);
```

## Caching Layout Information 缓存布局信息

## Take Elements Out of the Flow for Animations 将元素提出动画流
A technique to avoid a reflow of a big part of the page is to use the following steps:
使用以下步骤可以避免对大部分页面进行重排版：

1. Use absolute positioning for the element you want to animate on the page, taking it out of the layout flow of the page.动画元素使用绝对布局，脱离文档流
2. Animate the element. When it expands, it will temporarily cover part of the page. This is a repaint, but only of a small part of the page instead of a reflow and repaint of a big page chunk.使用动画
3. When the animation is done, restore the positioning, thereby pushing down the rest of the document only once.存储位置

## IE and :hover
## Event Delegation 事件托管

当页面中存在大量元素，而且每个元素有一个或多个事件句柄与之挂接（例如 onclick）时，可能会影响性能。连接每个句柄都是有代价的，无论其形式是加重了页面负担（更多的页面标记和 JavaScript 代码）还是表现在运行期的运行时间上。你需要访问和修改更多的 DOM 节点，程序就会更慢，特别是因为事件挂接过程都发生在 onload（或 DOMContentReady）事件中，对任何一个富交互网页来说那都是一个繁忙的时间段。挂接事件占用了处理时间，另外，浏览器需要保存每个句柄的记录，占用更多内存。当这些工作结束时，这些事件句柄中的相当一部分根本不需要（因为并不是 100%的按钮或者链接都会被用户点到），所以很多工作都是不必要的。

一个简单而优雅的处理 DOM 事件的技术是事件托管。它基于这样一个事实：事件逐层冒泡总能被父元素捕获。采用事件托管技术之后，你只需要在一个包装元素上挂接一个句柄，用于处理子元素发生的所有事件。

假设你要为图中所显示的文档提供一个逐步增强的 Ajax 体验。如果用户关闭了 JavaScript，菜单中的链接仍然可以正常地重载页面。但是如果 JavaScript 打开而且用户代理有足够能力，你希望截获所有点击，阻止默认行为（转入链接），发送一个 Ajax 请求获取内容，然后不刷新页面就能够更新部分页面。使用事件托管实现此功能，你可以在 UL"menu"单元挂接一个点击监听器，它封装所有链接并监听所有 click 事件，看看他们是否发自一个链接。

```JavaScript
document.getElementById('menu').onclick = function (e) {
  // x-browser target
  e = e || window.event;
  var target = e.target || e.srcElement;
  var pageid, hrefparts;
  // only interesed in hrefs
  // exit the function on non-link clicks
  if (target.nodeName !== 'A') {
    return;
  }
  // figure out page ID from the link
  hrefparts = target.href.split('/');
  pageid = hrefparts[hrefparts.length - 1];
  pageid = pageid.replace('.html', '');
  // update the page
  ajaxRequest('xhr.php?page=' + id, updatePageContents);
  // x-browser prevent default action and cancel bubbling
  if (typeof e.preventDefault === 'function') {
    e.preventDefault();
    e.stopPropagation();
  } else {
    e.returnValue = false;
    e.cancelBubble = true;
  }
};
```
跨浏览器部分包括： 访问事件对象，并判断事件源（目标）、 结束文档树上的冒泡（可选）、 阻止默认动作（可选，但此例中是必须的，因为任务是捕获链接而不转入这些链接）

# 第四章 Algorithms and Flow Control 算法和流程控制

## Loops 循环

### Types of Loops 循环的类型

for while do..while for...in

### Loop Performance 循环性能

循环性能争论的源头是应当选用哪种循环。在 JavaScript 提供的四种循环类型中，只有一种循环比其他循环明显要慢： for-in 循环。

由于每次迭代操作要搜索实例或原形的属性， for-in 循环每次迭代都要付出更多开销，所以比其他类型循环慢一些。在同样的循环迭代操作中， for-in 循环比其他类型的循环慢 7 倍之多。因此推荐的做法如下：除非你需要对数目不详的对象属性进行操作，否则避免使用 for-in 循环。如果你迭代遍历一个有限的，已知的属性列表，使用其他循环类型更快，可使用如下模式：
```javascript
var props = ["prop1", "prop2"],
i = 0;
while (i < props.length){
    process(object[props[i]]);
}
```

###  Decreasing the work per iteration 减少迭代的工作量

while倒序循环更优

### Decreasing the number of iterations 减少迭代次数

### Function-Based Iteration 基于函数的迭代

ECMA-262 标准第四版介绍了本地数组对象的一个新方法 forEach()。此方法遍历一个数组的所有成员，并在每个成员上执行一个函数。在每个元素上执行的函数作为 forEach()的参数传进去，并在调用时接收三个参数，它们是：数组项的值，数组项的索引，和数组自身。下面是用法举例：
```javascript
items.forEach(function(value, index, array){
  process(value);
});
```
尽管基于函数的迭代显得更加便利，它还是比基于循环的迭代要慢一些。每个数组项要关联额外的函数调用是造成速度慢的原因。

## Conditionals 条件表达式
### if-else Versus switch if-else 与 switch 比较
我们的自然倾向认为条件体较少时应使用 if-else 而条件体较多时应使用 switch 表达式，如果从性能方面考虑也是正确的。

### Optimizing if-else 优化 if-else
优化 if-else 的目标总是最小化找到正确分支之前所判断条件体的数量。**最简单的优化方法是将最常见的条件体放在首位。**
另外一种**减少条件判断数量的方法是将 if-else 组织成一系列嵌套的 if-else 表达式**。使用一个单独的一长串的 if-else 通常导致运行缓慢，因为每个条件体都要被计算。

### Lookup Tables 查表法
查表法最常用于一个键和一个值形成逻辑映射的领域（如前面的例子）。一个 switch 表达式更适合于每个键需要一个独特的动作，或者一系列动作的场合。

## Recursion 递归

### Call Stack Limits 调用栈限制
JavaScript 引擎所支持的递归数量与 JavaScript 调用栈大小直接相关。只有 Internet Explorer 例外，它的调用栈与可用系统内存相关，其他浏览器有固定的调用栈限制。

### Recursion Patterns 递归模式
调用自身 和 循环调用

### Iteration 迭代
任何可以用递归实现的算法都可以用迭代实现。迭代算法通常包括几个不同的循环，分别对应算法过程的不同方面，也会导致自己的性能为题。但是，使用优化的循环替代长时间运行的递归函数可以提高性能，因为运行一个循环比反复调用一个函数的开销要低。

### Memoization


# 第五章 Strings and Regular Expressions 字符串和正则表达式

## String Concatenation 字符串连接

![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/3.png)

当连接少量字符串时，所有这些函数都很快，临时使用的话，可选择最熟悉的使用。当合并字符串的长度和数量增加之后，有些函数开始显示出自己的威力。

### Plus (+) and Plus-Equals (+=) Operators
```javascript
str += "one" + "two";
```

此代码执行时，发生四个步骤：
1. 内存中创建了一个临时字符串。
2. 临时字符串的值被赋予“onetwo”。
3. 临时字符串与 str 的值进行连接。
4. 结果赋予 str

图 5-1 连接字符串时的内存使用情况： s1 复制到 s2 的尾部形成 s3；基本字符串 s2 没有被复制
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/4.png)


### Firefox and compile-time folding Firefox 和编译期合并
在赋值表达式中所有字符串连接都属于编译期常量， Firefox 自动地在编译过程中合并它们。现在很多浏览器都有这样的优化操作吧。

### Array Joining 数组联结
在大多数浏览器上，数组联结比连接字符串的其他方法更慢，但是事实上，为一种补偿方法，在 IE7 和更早的浏览器上它是连接大量字符串唯一高效的途径

### String.prototype.concat
原生字符串连接函数接受任意数目的参数，并将每一个参数都追加在调用函数的字符串上。这是连接字符串最灵活的方法，因为你可以用它追加一个字符串，或者一次追加几个字符串，或者一个完整的字符串数组。
不幸的是，大多数情况下 concat 比简单的+和+=慢一些，而且在 IE， Opera 和 Chrome 上大幅变慢。

## Regular Expression Optimization 正则表达式优化
粗浅地编写正则表达式是造成性能瓶颈的主要原因（后面“回溯失控”一节有一些例子说明这是多么严重的问题），但还有很多可以改进正则表达式效率的地方。两个正则表达式匹配相同的文本并不意味着他们具有同等的速度。

### How Regular Expressions Work 正则表达式工作原理
1. Compilation编译
当你创建了一个正则表达式对象之后（使用一个正则表达式直接量或者 RegExp 构造器），浏览器检查你的模板有没有错误，然后将它转换成一个本机代码例程，用于执行匹配工作。如果你将正则表达式赋给一个变量，你可以避免重复执行此步骤。

2. Setting the starting position设置起始位置
当一个正则表达式投入使用时，首先要确定目标字符串中开始搜索的位置。它是字符串的起始位置，或者由正则表达式的 lastIndex 属性指定，但是当它从第四步返回到这里的时候（因为尝试匹配失败），此位置将位于最后一次尝试起始位置推后一个字符的位置上。
浏览器厂商优化正则表达式引擎的办法是，在这一阶段中通过早期预测跳过一些不必要的工作。例如，如果一个正则表达式以^开头， IE 和 Chrome 通常判断在字符串起始位置上是否能够匹配，然后可避免愚蠢地搜索后续位置。另一个例子是匹配第三个字母是 x 的字符串，一个聪明的办法是先找到 x，然后再将起始位置回溯两个字符（例如，最近的 Chrome 版本实现了这种优化）。

3. Matching each regex token匹配每个正则表达式的字元
正则表达式一旦找好起始位置，它将一个一个地扫描目标文本和正则表达式模板。当一个特定字元匹配失败时，正则表达式将试图回溯到扫描之前的位置上，然后进入正则表达式其他可能的路径上。

4. Success or failure 匹配成功或失败
如果在字符串的当前位置上发现一个完全匹配，那么正则表达式宣布成功。如果正则表达式的所有可能路径都尝试过了，但是没有成功地匹配，那么正则表达式引擎回到第二步，从字符串的下一个字符重新尝试。只有字符串中的每个字符（以及最后一个字符后面的位置）都经历了这样的过程之后，还没有成功匹配，那么正则表达式就宣布彻底失败。

### Understanding Backtrack 理解回溯
当一个正则表达式扫描目标字符串时，它从左到右逐个扫描正则表达式的组成部分，在每个位置上测试能不能找到一个匹配。对于每一个量词和分支，都必须决定如何继续进行。如果是一个量词（诸如*， +?，或者{2,}），正则表达式必须决定何时尝试匹配更多的字符；如果遇到分支（通过|操作符），它必须从这些选项中选择一个进行尝试。

每当正则表达式做出这样的决定，如果有必要的话，它会记住另一个选项，以备将来返回后使用。如果所选方案匹配成功，正则表达式将继续扫描正则表达式模板，如果其余部分匹配也成功了，那么匹配就结束了。但是如果所选择的方案未能发现相应匹配，或者后来的匹配也失败了，正则表达式将回溯到最后一个决策点，然后在剩余的选项中选择一个。它继续这样下去，直到找到一个匹配，或者量词和分支选项的所有可能的排列组合都尝试失败了，那么它将放弃这一过程，然后移动到此过程开始位置的下一个字符上，重复此过程。

### Alternation and backtracking 分支和回溯
例子：
```JavaScript
/h(ello|appy) hippo/.test("hello there, happy hippo");
```
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/5.png)

### Repetition and backtracking 重复与回溯

```JavaScript
var str = "<p>Para 1.</p>" +
        "<img src='smiley.jpg'>" +
        "<p>Para 2.</p>" +
        "<div>Div.</div>";
/<p>.*<\/p>/i.test(str);
```
图 5-5 回溯与贪婪量词和懒惰量词
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/6.png)

### Runaway Backtracking 回溯失控
### The solution: Be specific 解决方法：具体化
### Emulating atomic groups using lookahead and backreferences 使用前瞻和后向引用列举原子组
### Nested quantifiers and runaway backtracking 嵌套量词和回溯失控
### From bad to worse. 从坏到更坏
### A Note on Benchmarking 测试基准说明
### More Ways to Improve Regular Expression Efficiency 提高正则表达式效率的更多方法

正则表达式处理慢往往是因为匹配失败过程慢，而不是匹配成功过程慢。

- Start regexes with simple, required tokens正则表达式以简单的，必需的字元开始
- Make quantified patterns and their following token mutually exclusive编写量词模板，使它们后面的字元互相排斥
- Reduce the amount and reach of alternation减少分支的数量，缩小它们的范围

分支使用 | ，竖线，可能要求在字符串的每一个位置上测试所有的分支选项。你通常可通过使用字符类和选项组件减少对分支的需求，或将分支在正则表达式上的位置推后（允许到达分支之前的一些匹配尝试失败）。下表列出这些技术的例子。

![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/highPerformanceJs/7.png)

字符类比分支更快，因为他们使用位向量实现（或其他快速实现）而不是回溯。当分支必不可少时，将常用分支放在最前面，如果这样做不影响正则表达式匹配的话。分支选项从左向右依次尝试，一个选项被匹配上的机会越多，它被检测的速度就越快。

- Use noncapturing groups使用非捕获组
- Capture interesting text to reduce postprocessing捕获感兴趣的文字，减少后处理
- Expose required tokens暴露所需的字元
- Use appropriate quantifiers使用适当的量词
- Reuse regexes by assigning them to variables 将正则表达式赋给变量，以重用它们
- Split complex regexes into simpler pieces将复杂的正则表达式拆分为简单的片断

### When Not to Use Regular Expressions 什么时候不应该使用正则表达式
小心使用它，正则表达式是非常快的。然而，当你只是搜索文字字符串时它们经常矫枉过正。尤其当你事先知道了字符串的哪一部分将要被测试时。

###  String Trimming 字符串修剪
####  Trimming with Regular Expressions 用正则表达式修剪
```javascript
if (!String.prototype.trim) {
	String.prototype.trim = function() {
		return this.replace(/^\s+/, "").replace(/\s+$/, "");
	}
}
```

####  Trimming Without Regular Expressions 不使用正则表达式修剪
```javascript
// trim 6
String.prototype.trim = function() {
	var start = 0,
		end = this.length - 1,
		ws = " \n\r\t\f\x0b\xa0\u1680\u180e\u2000\u2001\u2002\u2003\
	u2004\ u2005\ u2006\ u2007\ u2008\ u2009\ u200a\ u200b\ u2028\ u2029\ u202f\ u205f\ u3000\ ufeff ";
	while (ws.indexOf(this.charAt(start)) > -1) {
		start++;
	}
	while (end > start && ws.indexOf(this.charAt(end)) > -1) {
		end--;
	}
	return this.slice(start, end + 1);
}
```


####  A Hybrid Solution 混合解决方案

```javascript
// trim 7
String.prototype.trim = function() {
	var str = this.replace(/^\s+/, ""),
	end = str.length - 1,
	ws = /\s/;
	while (ws.test(str.charAt(end))) {
		end--;
	}
	return str.slice(0, end + 1);
}
```

#  第六章 Responsive Interfaces 响应接口

第一节介绍了浏览器 UI 线程概念。总的来说，大多数浏览器有一个单独的处理进程，它由两个任务所共享： JavaScript 任务和用户界面更新任务。每个时刻只有其中的一个操作得以执行，也就是说当 JavaScript代码运行时用户界面不能对输入产生反应，反之亦然。或者说，当 JavaScript 运行时，用户界面就被“锁定”了。管理好 JavaScript 运行时间对网页应用的性能很重要。
 
 ##  The Browser UI Thread 浏览器 UI 线程
 
JavaScript 和 UI 更新共享的进程通常被称作浏览器 UI 线程（虽然对所有浏览器来说“线程”一词不一定准确）。此 UI 线程围绕着一个简单的队列系统工作，任务被保存到队列中直至进程空闲。

##  Browser Limits 浏览器限制

浏览器在 JavaScript 运行时间上采取了限制。这是一个有必要的限制，确保恶意代码编写者不能通过无尽的密集操作锁定用户浏览器或计算机。此类限制有两个：**调用栈尺寸限制**（第四章讨论过）和**长时间脚本限制**。长运行脚本限制有时被称作长运行脚本定时器或者失控脚本定时器，但其基本思想是浏览器记录一个脚本的运行时间，一旦到达一定限度时就终止它。当此限制到达时，浏览器会向用户显示一个对话框。

##  Yielding with Timers 用定时器让出时间片

-  Timer Basics 定时器基础
 在 JavaScript 中使用 setTimeout()或 setInterval()创建定时器，两个函数都接收一样的参数：一个要执行的函数，和一个运行它之前的等待时间（单位毫秒）。 setTimeout()函数创建一个定时器只运行一次，而setInterval()函数创建一个周期性重复运行的定时器。
 
-  Timer Precision 定时器精度
 JavaScript 定时器延时往往不准确，快慢大约几毫秒。仅仅因为你指定定时器延时 250 毫秒，并不意味任务将在调用 setTimeout()之后精确的 250 毫秒后加入队列。所有浏览器试图尽可能准确，但通常会发生几毫秒滑移，或快或慢。正因为这个原因，定时器不可用于测量实际时间。
 在 Windows 系统上定时器分辨率为 15 毫秒，也就是说一个值为 15 的定时器延时将根据最后一次系统时间刷新而转换为 0 或者 15。设置定时器延时小于 15 将在 Internet Explorer 中导致浏览器锁定，所以最小值建议为 25 毫秒（实际时间是 15 或 30）以确保至少 15 毫秒延迟。
 
- Array Processing with Timers 在数组处理中使用定时器
 processArray()函数以一种可重用的方式实现了先前的模板，并接收三个参数：待处理数组，对每个项调用的处理函数，处理结束时执行的回调函数。
```JavaScript
function processArray(items, process, callback) {
	var todo = items.concat(); //create a clone of the original
	setTimeout(function() {
		process(todo.shift());
		if (todo.length > 0) {
			setTimeout(arguments.callee, 25);
		} else {
			callback(items);
		}
	}, 25);
}
```

##  Splitting Up Tasks 分解任务

 Timed Code 限时运行代码

 Timers and Performance 定时器与性能

##  Web Workers
自 JavaScript 诞生以来，还没有办法在浏览器 UI 线程之外运行代码。Web Workers API 改变了这种状况，它引入一个接口，使代码运行而不占用浏览器 UI 线程的时间。

- Worker Environment 运行环境
 因为Web Workers有不同的全局运行环境，你不能在 JavaScript 代码中创建。事实上，你需要创建一个完全独立的 JavaScript 文件，包含那些在Web Workers中运行的代码。要创建Web Workers，你必须传入这个JavaScript 文件的 URL：
```JavaScript
 var worker = new Worker("code.js");
```

-  Worker Communication 交互
Web Workers和网页代码通过事件接口进行交互。网页代码可通过 postMessage()方法向Web Workers传递数据，它接收单个参数，即传递给Web Workers的数据。此外，在Web Workers中还有 onmessage 事件句柄用于接收信息。例如：
```JavaScript
var worker = new Worker("code.js");
worker.onmessage = function(event) {
	alert(event.data);
};
worker.postMessage("Nicholas");
```
```JavaScript
//inside code.js
self.onmessage = function(event) {
	self.postMessage("Hello, " + event.data + "!");
};
```

- Loading External Files 加载外部文件
当Web Workers通过 importScripts()方法加载外部 JavaScript 文件，它接收一个或多个 URL 参数，指出要加载的 JavaScript 文件网址。Web Workers以阻塞方式调用 importScripts()，直到所有文件加载完成并执行之后，脚本才继续运行。由于工人线程在 UI 线程之外运行，这种阻塞不会影响 UI 响应。例如：
```JavaScript
//inside code.js
importScripts("file1.js", "file2.js");
self.onmessage = function(event) {
	self.postMessage("Hello, " + event.data + "!");
}
```

-  Practical Uses 实际用途
Web Workers适合于那些纯数据的，或者与浏览器 UI 没关系的长运行脚本。它看起来用处不大，而网页应用程序中通常有一些数据处理功能将受益于Web Workers，而不是定时器。
其它可能受益的任务如下：
1. Encoding/decoding a large string编/解码一个大字符串
2. Complex mathematical calculations (including image or video processing)复杂数学运算（包括图像或视频处理）
3. Sorting a large array给一个大数组排序

#  第七章 Ajax 异步 JavaScript 和 XML

###  Requesting Data 请求数据
- XMLHttpRequest (XHR)
- Dynamic script tag insertion 动态脚本标签插入
- iframes
- Comet
- Multipart XHR 多部分的 XHR

###  XMLHttpRequest
readyState 等于 4 表示整个响应报文已经收并完可用于操作。readyState 等于 3 则表示此时正在与服务器交互，响应报文还在传输之中。
```javascript
req.onreadystatechange = function() {
	if (req.readyState === 3) { // Some, but not all, data has been received.
		var dataSoFar = req.responseText;
		...
	} else if (req.readyState === 4) { // All data has been received.
		var data = req.responseText;
		...
	}
}
```
1. POST versus GET when using XHR. 使用 XHR 时，应使用 POST 还是 GET
 只有当 URL 和参数的长度超过了 2'048 个字符时才使用 POST 提取数据。因为 Internet 
Explorer 限制 URL的长度，过长将导致请求（参数）被截断。

2. Dynamic script tag insertion 动态脚本标签插入
该技术克服了 XHR 的最大限制：它可以从不同域的服务器上获取数据。这是一种黑客技术，而不是实例化一个专用对象，你用 JavaScript 创建了一个新脚本标签，并将它的源属性设置为一个指向不同域的URL。

3. Multipart XHR

##  Ajax Performance Guidelines Ajax 性能向导

### Cache Data 缓存数据

-  在服务器端，设置 HTTP 头，确保返回报文将被缓存在浏览器中。

-  在客户端，于本地缓存已获取的数据，不要多次请求同一个数据。

##### Setting HTTP headers 设置 HTTP 头
如果你希望 Ajax 响应报文能够被浏览器所缓存，你必须在发起请求时使用 GET 方法。但这还不充分，你必须在响应报文中发送正确的 HTTP 头。 Expires 头告诉浏览器应当缓存响应报文多长时间。其值是一个日期，当过期之后任何对该 URL 发起的请求都不再从缓存中获得，而要重新访问服务器。一个 Expires 头如下：

```
Expires: Mon, 28 Jul 2014 23:30:00 GMT
```
 一个 Expires 头是确保浏览器缓存 Ajax 响应报文最简单的方法。

#####  Storing data locally 本地存储数据

#####  Know the Limitations of Your Ajax Library 了解 Ajax 库的限制