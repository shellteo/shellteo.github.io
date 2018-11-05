---
layout: post
title:  "不定宽高元素垂直水平居中小结"
date:   2017-08-16
categories: css
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article4/15252540236_4ac6b67460_z.jpg"
description: 使用css对不定宽高的元素进行垂直水平居中！
---
---
## 0.前言

很多文章都把不定宽高的的元素垂直水平居中讲得太复杂，最近做项目都有用到的方法和一些以前看到过记录下来的方法进行一个总结，如果你还有好的方法，欢迎进行交流。

先定义下面各种方法会用到的html结构
```html
<div class="container">
    <div class="center">
    </div>
</div>
```

## 1.绝对定位配合transform属性
```css
html,body{
    height: 100%;
}
.container{
    width: 100%;
    height: 100%;
    background: #000000;
}
.center{
    width: 20%;
    height: 20%;
    background: #FFFFFF;
    position: absolute;
    top: 50%;
    left:50%;
    transform: translate(-50%, -50%);
}
```
transform属性简介：
CSS transform 属性允许你修改CSS视觉格式模型的坐标空间。
使用它，元素可以被转换（translate）、旋转（rotate）、缩放（scale）、倾斜（skew）。
通过 translate() 方法，元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数
但是使用的时候需要考虑transform属性的兼容性。

## 2.使用absolute绝对定位原理
元素在过度受限情况下，将margin设置为auto，
浏览器会重算margin的值，过度受限指的是同时设置top/bottom与height或者left/right与width。
```css
.center{
  position:absolute;
  width:200px;
  height:200px;
  top:0;bottom:0;left:0;right:0;
  margin:auto;
  background:#518fca;
}
```

## 3.使用table-cell属性
这里修改了html的结构，在center里面新加了class为box的div，然后设置box的css样式，table-cell里面的vertical-align和text-align两个属性，只对行内元素有效，所有将box的display设置为inline-block，打开浏览器，可以看到box的div能够垂直水平居中。
```html
//html结构
<div class="container">
    <div class="center">
        <div class="box"></div>
    </div>
</div>
```
以下是css代码：
```css
.container{
    display: table;
    width: 100%;
    height: 100%;
    background-color: #000000;
}
.center{
    display: table-cell;
    vertical-align: middle;
    text-align: center;
}
.box{
    display: inline-block;
    width: 20%;
    height: 20%;
    background-color: #FFFFFF;
}
```
## 4.flex弹性盒布局
如果想要了解flex详情，mdn上有详细介绍，地址：https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes
```css
.container{
    width:100%;
    height: 100%;
    background-color:#000000;
    display: flex;
    justify-content: center;
    align-items: center;
}
.center{
    display: block;
    width: 20%;
    height: 20%;
    background-color: #FFFFFF;
}
```
