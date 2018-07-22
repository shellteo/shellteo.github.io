---
layout: post
title:  "后jQuery时代"
date:   2017-08-04
categories: jquery
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article1/b1c9fee0c17ab8a80b7df47ec548a074.jpg?Expires=1532234846&OSSAccessKeyId=TMP.AQEz0I30vhhHDJiuB3S1tGTeblimsDIlxIVsYYJgwh2vPI1LJvlVayoKCP1xADAtAhUAp-Gv8EX4_10m9HMj43EzwibxWfICFHSwr8wjnJ5PFHQ9WHMh_KC0xm9D&Signature=Vc%2Fk0kYaiAu%2F5rnIeeorpyCL760%3D"
description: 前端工程化的今天，操作DOM在所难免，但是如何像jQuery一样优雅的操作DOM，这是我们需要去考虑的.
---
---
## 前言

&emsp;&emsp;在MV*框架兴起，前端告别刀耕火种进入工程化的今天，还没来得及学习Angular，Facebook就带着react框架降临，不久vue.js又在这些框架打得火热的时候脱颖而出，各种框架充斥着我们的视觉神经，好像操作DOM来写前端的思想已经退出了历史舞台。而jQuery作为那个年代前端er主要的库，对于DOM的增删改查的便利让我们对jQuery顶礼膜拜，jQuery为了简化js而生，却又因简化js而死，jQuery发布第一版距今已经11年之久，而后jQuery时代，我们需要怎么做？

&emsp;&emsp;第一版jQuery诞生于2006年，当初jQuery的诞生有两个目的，第一简化DOM操作，第二减少开发过程中跨浏览器的问题。

&emsp;&emsp;他的创始人John Resig给这些代码添加了注释，如下有第一版jQuery的地址，你可以第一版的源码。https://genius.it/johnresig.com/files/jquery-original.html

&emsp;&emsp;jQuery是伟大的，但是在当下技术发展的今天，我们不需要再兼容IE6和IE7的情况下，很多兼容性问题浏览器已经自行解决了，所以说，将jQuery从你的网站中删除是正确的做法。而且js现在已经更加简洁，对于很多DOM操作，用原生js来写也会非常简洁。

## jQuery速览

**1.文档载入后执行：**
```javascript
$(document).ready(function(){})
$(function(){})
```

**2.jQuery事件绑定**
`on`、`bind`、`live`、`delegate`

**3.jQuery增删改查**

**增加：**
`append()` - 在被选元素的结尾插入内容
`prepend()` - 在被选元素的开头插入内容
`after()` - 在被选元素之后插入内容
`appendTo()` - 把所有匹配的元素追加到指定的元素元素集合中。

**删除：**
`remove()` - 删除被选元素（及其子元素）
```javascript
$("p").remove(".italic");
```

`empty()` - 从被选元素中删除子元素
```javascript
$("#div1").empty();
```

**修改：**
设置内容- `text()`、`html()` 以及 `val()`
`text()` - 设置或返回所选元素的文本内容
```javascript
$("#test1").text("Hello world!");
```

`html()` - 设置或返回所选元素的内容（包括 HTML 标记）
```javascript
$("#test2").html("Hello world!");
```

`val()` - 设置或返回表单字段的值
```javascript
$("#test3").val("Dolly Duck");
```

设置属性- `attr()`
jQuery `attr()` 方法也用于设置/改变属性值。
attr设置多个值
```javascript
$("#w3s").attr({
    "href" : "http://www.w3school.com.cn/jquery",
    "title" : "W3School jQuery Tutorial"
});
```

**获取：**
获得内容- `text()`、`html()` 以及 `val()`**粗体文本**
`text()` - 设置或返回所选元素的文本内容
`html()` - 设置或返回所选元素的内容（包括 HTML 标记）
`val()` - 设置或返回表单字段的值

获取属性- `attr()`
jQuery attr() 方法用于获取属性值
```javascript
$("#w3s").attr("href")
```

**查找：**
1.基本过滤器：
a) `:first`，选取第一个元素，别忘记它也是被放在一个集合里哦！因为JQuery它是DOM对象的一个集合。如，“$("tr:first")”返回所有tr元素的第一个tr元素，它仍然被保存在集合中。

b) `:last`，选取最后一个元素。如:“$("tr:last")”返回所有tr元素的最后一个tr元素，它仍然被保存在集合中。

c)`:even`、`:odd`奇偶过滤器

d)`:not`选择所有不符合的元素

e)`:eq`选择索引匹配的元素

2.子元素过滤器
`:first-child`（`:last-child`）选择所有父级元素下的第一个（最后一个）子元素。
`:first-of-type`（`:last-of-type`）选择所有相同的元素名称的第一个（最后一个）兄弟元素
`:nth-child(n)` 选择的他们所有父元素的第n个子元素。
`:nth-last-child(n)`选择所有他们父元素的第n个子元素。计数从最后一个元素开始到第一个。
`:nth-of-type(n)` 选择同属于一个父元素之下，并且标签名相同的子元素中的第n个。
`find(selector)` 查找该节点所有的子孙节点
`children(selector)`查找所有的子节点，不过该方法只会返回直接的子节点，不会返回所有的子孙节点

3.父元素过滤器
`parent(selector)` 查找父元素，可传入selector进行过滤(下同)
`parents(selector) `查找所有的祖先节点

4.兄弟元素过滤器
`siblings()` 查找该节点所有的兄弟节点，不分前后
`prev()`查找该节点的上一个兄弟节点
`prevAll()`查找该节点之前所有的节点
`next()`查找该节点的下一个兄弟节点
`nextAll()`查找该节点之后所有的节点

## 去除DOM依赖

如何在项目中去除jQuery依赖，以及如何使用原生js替代jQuery中方法？
**1.查找DOM**
`getElementById()`：通过id查找元素，访问DOM最快的方法，而且大部分浏览器都支持
`getElementsByClassName()`：通过class查找元素
注：上面两个方法很快，但由于仅通过类名选择元素的限制，作用有限。
```javascript
//polyfill，实现getElementsByClassName
function getElementsByClassName(className) {
    var all = document.all ? document.all : document.getElementsByTagName('*');
    var elements = new Array();
    for (var e = 0; e < all.length; e++) {
        if (all[e].className == className) {
            elements[elements.length] = all[e];
            break;
        }
    }
    return elements;
}
```

`querySelector()`和`querySelectorAll()`：匹配指定 CSS 选择器元素的第一个子元素（全部元素）。
注：`querySelectorAll`获取到的是一个类数组，可以通过`Array.prototype.slice.call()`将类数组转为数组。
```javascript
// jquery选择器部分实现，container参数可选，获取全部符合的元素
function $(selector, container) {
  return (container || document).querySelectorAll(selector);
}
// 获取一个符合的元素
function $one(selector, container) {
  return (container || document).querySelector(selector);
}
```

`getElementsByTagName`：通过标签名查找元素，跨浏览器安全快速的方法，获取到一个类数组。

**2.查找遍历**
获取父类元素-`parentNode`
获取子类元素-`childNode`，`firstChild`，`lastChild`
获取同级元素-`nextSibling`、`previousSibling`，只获取一个匹配的
```javascript
//获取全部的同级元素
function getSiblings(el, filter) {
    var siblings = [];
    el = el.parentNode.firstChild;
    do { if (!filter || filter(el)) siblings.push(el); } while (el = el.nextSibling);
    return siblings;
}
```

匹配特定选择器且离当前元素最近的祖先元素-`closest()`[实验中的功能，有的浏览器不兼容]
```javascript
// polyfill，不兼容浏览器的实现方法
this.Element && function(ElementPrototype) {
    ElementPrototype.closest = ElementPrototype.closest ||
        function(selector) {
            var el = this;
            while (el.matches && !el.matches(selector)) el = el.parentNode;
            return el.matches ? el : null;
        }
}(Element.prototype);
```

`matches()`:如果元素将被指定的选择器字符串选择，`Element.matches()` 方法返回`true`; 否则返回`false`。
```javascript
//polyfill
if (!Element.prototype.matches) {
    Element.prototype.matches =
        Element.prototype.matchesSelector ||
        Element.prototype.mozMatchesSelector ||
        Element.prototype.msMatchesSelector ||
        Element.prototype.oMatchesSelector ||
        Element.prototype.webkitMatchesSelector ||
        function(s) {
            var matches = (this.document || this.ownerDocument).querySelectorAll(s),
                i = matches.length;
            while (--i >= 0 && matches.item(i) !== this) {}
            return i > -1;
        };
}
```

**3.DOM操作**
创建DOM-`createElement`
替换DOM-`replaceChild`
删除DOM（从DOM中删除元素的父元素，使元素的内容保持原样）
```javascript
/*
 *删除element，但是保留element里面的子元素的做法
 */
var element = document.querySelector('.container');
// 获取元素的父元素
var parent = element.parentNode;
// 遍历将所有子元素从这个元素中移出去
while (element.firstChild) parent.insertBefore(element.firstChild, element);
// 删除这个元素
parent.removeChild(element);
清空元素的内容-DOM中删除元素的所有子节点。
var el = document.querySelector('div');
//通过设置innerHTML为空清空所有子节点
el.innerHTML = '';
删除DOM-removeChild方法从DOM中删除一个子节点。返回删除的节点。
//从parent中删除child，函数的返回值也是child的引用
parent.removeChild(child);
插入DOM-insertBefore、appendChild
//insertBefore，在parent节点中将child节点插入到node1之前
parent.insertBefore(child, node1);
//appendChild，将child节点插入到parent的最尾部
parent.appendChild(child)
```

获取元素的text内容-`textContent`、`innerText`（IE8）
```javascript
let node = document.querySelector('container');
let text = node.textContent || node.innerText;
```

获取/设置元素的HTML内容-`innerHTML`属性
复制当前节点（或者复制当前节点以及它的所有子孙节点）-`cloneNode()`


**4.属性操作-用于获取和设置元素的DOM属性的功能**

设置、获取和删除DOM元素属性
原生JavaScript可一直接访问元素的属性，例如：`href`、`title`、`alt`、`value`，删除这些属性的可以使用`delete`关键字
```javascript
var node = document.querySelector('selector');
//设置属性property
node.property = ''
//使用delete删除property属性
delete node.property;
添加，删除和测试class
//判断node是否有className
function hasClass(node, className) {
    return node.classList ?
        node.classList.contains(className) : new RegExp('\\b' + className + '\\b').test(node.className);
}
//添加class
function addClass(node, className) {
    if (node.classList) {
        node.classList.add(className);
    }
    else if (!hasClass(node, className)) {
        node.className += ' ' + className;
    }
}
//移除class
function removeClass(node, className) {
    if (node.classList) {
        node.classList.remove(className);
    }
    else {
        node.className = node.className.replace(new RegExp('\\b' + className + '\\b', 'g'), '');
    }
}
```

获取，设置和删除属性-`getAttribute`、`setAttribute`、`removeAttribute`
```javascript
let node = document.querySelector('selector');
//在node上设置myProp属性值为mydata
node.setAttribute('myProp', 'mydata');
//获取myProp属性
console.log(node.getAttribute('myProp'));
//删除myProp属性
node.removeAttribute('myProp');
```

**5.获取设置元素样式**

样式获取-`getComputedStyle`（IE9以下:`currentStyle`）
```javascript
var node = document.querySelector('selector');
// IE上使用currentStyle
var style = window.getComputedStyle ? getComputedStyle(node, null) : node.currentStyle;
```

样式设置-  `style`、`cssText`
```javascript
function setCss(node, styles) {
    for (var property in styles){
        node.style[property] = styles[property];
    }
}
//使用cssText能够同时设置多个样式
node.style.cssText += 'color:red;'
```

各种位置获取如下：
获取并设置元素的滚动位置-`scrollTop`、`scrollLeft`
获取元素相对于其父元素的偏移位置-`offsetLeft`、`offsetTop`
获取相对于文档的元素的位置-`getBoundingClientRect`
```javascript
function offset(node) {
  let rect = node.getBoundingClientRect(),
  scrollLeft = window.pageXOffset || document.documentElement.scrollLeft,
  scrollTop = window.pageYOffset || document.documentElement.scrollTop;
  return { top: rect.top + scrollTop, left: rect.left + scrollLeft }
}
```

获取元素的宽度和高度-根据盒模型，元素包含`margin`、`border`、`padding`、`content`（`width`+`height`）
获取`content`+`padding`+`border`：`offsetWidth`/`offsetHeight`
获取`content`+`padding`:`clientWidth`/`clientHeight`
获取`margin`、`border`、`padding`的值可以通过`getComputedStyle`
获取窗口的宽高：`window.innerWidth`、`window.innerHeight`
获取文档的宽高：`document.documentElement.clientWidth/Height`、

**6.原生ajax**
`get`请求和`post`请求
```javascript
function getRequest(url, success) {
    var xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');
    xhr.open('GET', url);
    xhr.onreadystatechange = function () {
        if (xhr.readyState > 3 && xhr.status == 200) success(xhr.responseText);
    };
    xhr.setRequestHeader('X-Requested-With', 'XMLHttpRequest');
    xhr.send();
    return xhr;
}
function postRequest(url, data, success) {
    var params = typeof data == 'string' ? data : Object.keys(data).map(
        function(k){ return encodeURIComponent(k) + '=' + encodeURIComponent(data[k]) }
    ).join('&');
    var xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP");
    xhr.open('POST', url);
    xhr.onreadystatechange = function() {
        if (xhr.readyState>3 && xhr.status==200) { success(xhr.responseText); }
    };
    xhr.setRequestHeader('X-Requested-With', 'XMLHttpRequest');
    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    xhr.send(params);
    return xhr;
}
```

异步加载js，`async`或者`defer`关键字也可以异步
```javascript
//1.通过动态插入script标签异步加载
let scriptNode = document.createElement('script'),
  htmlNode = document.getElementsByTagName('script')[0];
scriptNode.src = url;
scriptNode.parentNode.insertBefore(scriptNode, htmlNode);
//2.通过async和defer属性来异步加载
<script src="" async defer></script>
```

**7.原生事件**
阻止冒泡：
```javascript
window.event? window.event.cancelBubble = true : e.stopPropagation();
```
阻止默认事件：
```javascript
window.event? window.event.returnValue = false : e.preventDefault();
```

获取键盘点击：
```javascript
var key = window.event ? event.keyCode : event.which;
console.log(key);
```

获取鼠标点击位置：
```javascript
function handler(event) {
    event = event || window.event;
    let pageX = event.pageX;
    let pageY = event.pageY;
    // IE 8
    if (pageX === undefined) {
        pageX = event.clientX + document.body.scrollLeft + document.documentElement.scrollLeft;
        pageY = event.clientY + document.body.scrollTop + document.documentElement.scrollTop;
    }
    return {
        pageX:pageX,
        pageY:pageY
    }
}
```

事件绑定和解绑:
```javascript
//添加事件
function addEvent(node, type, handler) {
    if (node.attachEvent) {
        node.attachEvent('on'+type, handler);
    } else {
        node.addEventListener(type, handler);
    }
}
//解除绑定
function removeEvent(node, type, handler) {
    if (node.detachEvent) {
        node.detachEvent('on'+type, handler);
    } else {
        node.removeEventListener(type, handler);
    }
}
```

文档加载事件：
```javascript
//对应于$(document).ready()的原生实现
document.addEventListener('DOMContentLoaded', function(){});
```

（end）