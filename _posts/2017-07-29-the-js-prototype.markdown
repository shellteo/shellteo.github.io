---
layout: post
title:  "关于原型链那些你所不知道的"
date:   2017-07-29
categories: js
author: 张翔
location: ShangHai, China
description: 介绍javascript原型链和一些原型链方法，想要真正掌握javascript，那么掌握原型链是必不可少的。
---
---
## 1.原型链简介
 ```javascript
 function car(price) {
 this.price = price;
 this.wheel = 4;
 }
 car.prototype.getWheel = function(){
 console.log(this.wheel);
 return this.wheel;
 }
 var bmw = new car(1000);
 ```
 `var bmw = new car(1000);` 语句， JavaScript 就会设置：`bmw.__proto__ = car.prototype`（**在内存中创建对象后，并在运行 this 绑定的函数 car()之前**）
 然后在你访问实例的属性时，JavaScript 首先检查它们是否直接存在于该对象中（即是否是该对象的自身属性），如果不是，它会在 `__proto__`中查找。
 因此，当你执行：
 ```javascript
 var bmw = new car(1000);
 ```
 JavaScript 实际上执行的是,下图有更加详细的解释：
 ```javascript
 var bmw = new Object();
 bmw.__proto__ = car.prototype;
 car.call(bmw);
 ```
 
 ## 2.对象常用方法
 #### 1.Object.prototype.hasOwnProperty()
 `hasOwnProperty()`函数用于指示一个对象自身(不包括原型链)是否具有指定名称的属性。如果有，返回true，否则返回false。
 
 #### 2.for in循环
 `for in`可以获取对象的所有可以枚举的属性，包括继承的属性
 
 #### 3.Object.keys()
 `Object.keys()` 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 `for...in` 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。
 
 #### 4.Object.getOwnPropertyNames()
 `Object.getOwnPropertyNames()`方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性）组成的数组。
 
 #### 5.Object.assign()
 `Object.assign()` 方法用于将所有可枚举的属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。
 
 #### 6.Object.defineProperty()和Object.defineProperties()
 `Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。
 ```javascript
 var o = {a:1, b:2};
 Object.defineProperty(o, 'd', {
   value: 4,
   enumerable: false,
   writable: false,
   configurable: false
 });
 ```
 #### 7.Object.prototype.isPrototypeOf()
 `isPrototypeOf()` 方法用于测试一个对象是否存在于另一个对象的原型链上。
 
 #### 8.instanceof
 `instanceof` 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。(是否是他的实例)
 
 #### 9.Object.is()
 `Object.is()` 方法确定两个值是否是相同的值。
 Object.is()与===号不同的地方在于
 ```javascript
 console.log(
     Object.is(NaN,NaN),
     NaN === NaN,
     Object.is(0,-0),
     0 === -0
 );
 ```
 
 ## 3.JS原型使用
 
 ### 1.js继承
 
 一、 prototype模式
 如果"audi"的prototype对象，指向一个car的实例，那么所有"audi"的实例，就能继承car了。
 ```javascript
 function car(price) {
     this.price = price;
     this.wheel = 4;
 }
 car.prototype.getWheel = function(){
     console.log(this.wheel);
     return this.wheel;
 }
 function audi(price,type){
     this.price = price;
     this.type = type;
 }
 //audi的prototype指向car的一个实例
 audi.prototype = new car();
 //构造器指回audi
 audi.prototype.constructor = audi;
 var audi_1 = new audi(4000,'1');
 console.log(audi_1.wheel);
 ```
 
 二、es6 引入了一套新的关键字用来实现 class。使用基于类语言的开发人员会对这些结构感到熟悉，但它们是不一样的。 JavaScript 仍然是基于原型的。这些新的关键字包括 `class`, `constructor`, `static`, `extends`, 和 `super`.
 ```javascript
 class benz extends car {
   constructor(price) {
      //super调用父类的构造函数
      super(price);
       this.wheel = 10;
   }
   get getPrice(){
       return this.price;
   }
 }
 ```
 
 ### 2.jquery无需new的实现方法
 ```javascript
 (function () {
     //
     var _jQuery = window.jQuery,
         _$ = window.$;
     var version = '0.0.1',
         jQuery = function (selector) {
             return document.querySelector(selector) ;
         };
     jQuery.prototype = {
         jquery:version,
         constructor:jQuery
     };
     window.$ = window.jQuery = function(selector){
         return new jQuery(selector);
     }
 })()
 ```
 
 ### 3.js数组判断
 ```javascript
 var arr = [1,2,3,4,5];
 
 //typeof是判断基础数据类型
 console.log(typeof arr);
 
 //instanceof方法，子页面上会发生兼容性问题
 console.log(arr instanceof Array);
 
 //原型链方法，问题兼容性
 console.log(arr.__proto__.constructor == Array);
 console.log(arr.constructor == Array);
 
 //通用方法
 function isArray(arr){
   return Object.prototype.toString.call(arr)=='[object Array]';
 }
 console.log(isArray(arr));
 //原生方法
 console.log(Array.isArray(arr));
 ```