---
layout: post
title:  "《Node.js实战》读书笔记"
date:   2015-10-10
categories: Other
author: 张翔
location: NanChang, China
cover: ""
description: 读书笔记
---
---
node.js如何避免污染全局命名空间

nodejs中查找模块的步骤

事件监听器和事件发射器

选择合适的存储机制取决于以下五个因素：
1. 存储什么数据
2. 为了保证性能，要多快的数据读取和写入速度
3. 有多少数据
4. 要怎么查询数据
5. 数据要保存多久，对可靠性有什么要求

splice()方法
```javascript
arrayObject.splice(index,howmany,item1,.....,itemX)
```
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/1.png)


slice()方法
```javascript
arrayObject.slice(start,end)
```
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/2.png)

    
#### 关系型数据库
- mysql是最流行的SQL数据库，Node社区对它的支持很好。
- PostgreSQl因其与标准的兼容性和健壮性受到认可，
- NoSQL数据库：通常也不需要预先定义schema
- Redis非常适合处理那些不需要长期访问的简单数据存储，Redis把数据存在RAM中，访问速度非常快，但是存储空间很有限，Redis是高性能数据处理原语，提供了实用的原语命令集（http://redis.io/commands）
- MongoDB是更通用的实用数据库

connect()自带的中间件
```javascript
function signedCookie(str, secret) {
	if (typeof str !== 'string') {
		return undefined;
	}
	if (str.substr(0, 2) !== 's:') {
		return str;
	}
	var secrets = !secret || Array.isArray(secret) ?
		(secret || []) :
		[secret];
	for (var i = 0; i < secrets.length; i++) {
		var val = signature.unsign(str.slice(2), secrets[i]);
		if (val !== false) {
			return val;
		}
	}
	return false;
}
```


a=a || {}; js中这句话的意思是什么？

这句话的意思是：如果a具有真值（不是undefined,null,NAN,false,0中的任意一种），则这个a可以被使用。否则将a定义为一个空的object对象{}。这样做的目的是，如果a没有被定义，在下面的代码里如果使用到变量a，浏览器将会抛处xxx is undefined的异常。大多数浏览器处理此异常的方法是停止执行其后的js代码。所以预先处理好可能发生这种异常的情况，属于一种异常处理机制。

split() 方法用于把一个字符串分割成字符串数组。
slice() 方法可从已有的数组中返回选定的元素。


7.1.4 query( )： 查询字符串解析
//query()查询字符串解析
//bodyParser()可以解析表单的POST请求，但GET请求怎么解析呢？用query()中间件
下面程序用到了query（）中间件，它会将请求发送过来的查询字符串以 JSON 格式作为响应返回，


windows下全局安装nexpress
npm install -g express-generator  后就解决了。

第三章
内容
1.用模块组织代码
2.编程规范
3.用回调处理一次性完结的事件
4.用事件发射器处理重复性事件
5.实现串行和并行的流程控制
6.使用流程控制工具

创建一个模块：exports
引入一个模块：require，注意require是node中少见的同步I/O操作
module.exports和exports的区别？
```javascript
/**
* Created by Zx on 2015/12/3.
*/
var Currency = function (canadianDollar) {
this.canadianDollar = canadianDollar;
}
Currency.prototype.roundTwoDecimals = function (amount) {
return Math.round(amount*100)/100;
}
Currency.prototype.canadianToUs = function (canadian) {
return this.roundTwoDecimals(canadian*this.canadianDollar);
}
Currency.prototype.USTocanadian = function (us) {
return this.roundTwoDecimals(us/this.canadianDollar);
}
exports = Currency;     
```
