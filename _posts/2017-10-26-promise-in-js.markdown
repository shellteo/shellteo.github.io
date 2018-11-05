---
layout: post
title:  "从微信小程序看Promsie.all和Promise顺序执行"
date:   2017-10-26
categories: js
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article15/promise.jpg"
description: 关于promise的方法在微信小程序中的使用，以及promise如何顺序执行处理请求
---
---
## 一、前言

最近在做小程序的开发，碰到的一个需求就是**表单提交**，提交的表单中包含有图片，微信这边的做法是先上传图片，后台把图片名称和**地址返回**给你，然后你把图片信息插入到表单的相应位置**再提交表单**，这里就涉及到如何上传完图片的请求再上传表单，而且微信小程序里面如果图片是多个的话，也只能一张张上传。简单来说就是**上传完图片**（多个请求），拿到返回值，再上传表单，该如何做？

## 二、Promise.all和Promise.race

#### 先来介绍**Promise.all**和**Promise.race**方法的不同点

`Promise.all(iterable)` 方法指当所有在可迭代参数中的 promises 已完成，或者第一个传递的 promise（指 reject）失败时，返回 promise。iterable为可迭代对象，但是一般为数组。返回值也是一个Promise对象。
需要明确的几点，Promise.all是**并发执行**的同时运行多个Promise对象，而且返回的Promise对象的参数是一个**数组**，数组中的各项也是可迭代对象执行的**顺序返回**。

`Promise.race(iterable)` 方法返回一个新的 promise，参数iterable中只要有一个promise对象"完成（resolve）"或"失败（reject）"，新的promise就会立刻"完成（resolve）"或者"失败（reject）"，并获得之前那个promise对象的返回值或者错误原因。所以只要iterable中有一个**完成或者失败就立即返回一个promise对象**。根据race这个单词为赛跑也能得出，最先到达的立即返回一个promise对象。

根据上面的定义，我们采用的Promise.all方法来完成我们的需求。

```javascript
//存储promise对象的数组
let promiseArr = [];
//图片地址数组
let imageList = [];
//将图片地址的上传的promise对象加入到promiseArr
for (let i = 0; i < imageList.length; i++) {
    let promise = new Promise((resolve, reject) => {
        //微信图片上传
        wx.uploadFile({
            url: 'https://xxx.xxx.xxx/api/uploadImage',
            filePath: imageList[i],
            name: 'file',
            success: function(res) {
                //可以对res进行处理，然后resolve返回
                resolve(res);
            },
            fail: function (error) {
                reject(error);
            },
            complete: function (res) {
            },
        })
    });
    promiseArr.push(promise)
}
//Promise.all处理promiseArr数组中的每一个promise对象
Promise.all(promiseArr).then((result) => {
    //对返回的result数组进行处理
})
```

## 三、微信小程序的问题

在做微信小程序的图片上传功能，这边只能先上传图片，然后将图片名和地址以response返回。

这里面我们就是用了promise.all方法但是有一个问题就是，promise.all是并发执行的，但是微信小程序一次只能并发10个请求。

对于图片上传，可能需要一次上传超过10张图片，也就是一次并发超过10个请求，这样的话微信就会报错*“WAService.js:4 uploadFile:fail createUploadTask:fail exceed max upload connection count 10”*。

## 四、顺序Promise执行处理

因为Promise.all是同时运行多个promsie对象，所以对于这种并发的数量，小程序是有限制的，一次只能并发10个，所以如果想突破这种限制，可以进行顺序执行每个Promise。
代码如下：

```javascript
//顺序处理函数
function sequenceTasks(tasks) {
    //记录返回值
    function recordValue(results, value) {
        results.push(value);
        return results;
    }
    let pushValue = recordValue.bind(null, []);
    let promise = Promise.resolve();
    // 处理tasks数组中的每个函数对象
    for (let i = 0; i < tasks.length; i++) {
        let task = tasks[i];
        promise = promise.then(task).then(pushValue);
    }
    return promise;
}

//函数数组，每个函数的返回值是一个promise对象
let promiseFuncArr = [];
//图片地址数组
let imageList = [];
//将图片地址的上传的函数加入到promiseFuncArr数组中
for (let i = 0; i < imageList.length; i++) {
    let promiseTemp = function(){
        return new Promise((resolve, reject) => {
            //微信图片上传
            wx.uploadFile({
                url: 'https://xxx.xxx.xxx/api/uploadImage',
                filePath: imageList[i],
                name: 'file',
                success: function(res) {
                    //可以对res进行处理，然后resolve返回
                    resolve(res);
                },
                fail: function (error) {
                    reject(error);
                },
                complete: function (res) {
                },
            })
        });
    };
    promiseFuncArr.push(promiseTemp)
}

sequenceTasks(promiseFuncArr).then((result) => {
    //对返回的result数组进行处理
});
```

#### 1.这里解释一下sequenceTasks函数的作用
首先recordValue函数传入两个值，一个是results是返回的数组，另一个是value，value是传入的值，results.push(value);将每一个值push到results数组，然后再返回results数组。

`let pushValue = recordValue.bind(null, []);`
pushValue也是一个函数对象，将recordValue bind到一个[ ]数组中，第一个参数传null代表，不改变函数this的指向，所以pushValue得到就是一个function (value)的函数，参数传入value。

`promise = promise.then(task).then(pushValue);`
task是函数，函数返回promise对象，在我们这里就是上传图片函数，每一张图片上传都创建一个函数，then(pushValue)，pushValue是function (value)的函数，value代表的就是图片上传之后的返回值，pushValue将返回值push到result数组中，依次执行，依次加入到result数组中，最后返回。就可以得到一个对象数组，数组中就是依次执行返回的结果。

#### 2. sequenceTasks也里面的for循环，也可以写成如下的reduce方式：

```javascript
function sequenceTasks(tasks) {
    //记录返回值
    function recordValue(results, value) {
        results.push(value);
        return results;
    }
    let pushValue = recordValue.bind(null, []);
    return tasks.reduce(function (promise, task) {
        return promise.then(task).then(pushValue);
    }, Promise.resolve());
}
```