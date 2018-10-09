---
layout: post
title:  "那些年做小程序遇到的问题"
date:   2018-08-01
categories: JS
author: 张翔
location: ShangHai, China
cover: "https://i.loli.net/2018/08/02/5b63293e7f896.jpg"
description: 之前做过一年的小程序，期间碰到了很多问题，这里做一个总结，很多问题，不知道微信有没有去解决。这里只是做一个简单明了的记录。
---
---
之前做过一年的小程序，期间碰到了很多问题，这里做一个总结，很多问题，不知道微信有没有去解决。这里只是做一个简单明了的记录。
### 一、看图片和选择图片确定之后触发onshow的bug
解决方法如下：
1.data或者全局里面定义一个变量

```javascript
data: {
    notShow: false
}
```

2.onshow事件**最上面**中添加下面代码，如果notshow为true，就直接return，**不触发onshow里面的其他代码**。

```javascript
onShow() {
    if (this.notShow) {
        this.setData({ notShow: false });
        return;
    }
}
```

3.在wx.previewImage或者wx.chooseImage事件调用之前插入下面代码，这样在执行onShow事件的时候，就会直接跳出来，不触发其他代码

```javascript
this.setData({ notShow: true})
```

### 二、微信上传线程突破九个限制
很多时候我们我们上传直接用的**Promise.all**，**微信一次只能发9个请求**，所以上传图片最多只能是9个图片。Promise.all 方法会**同时运行**多个promise对象，想要突破限制，核心思想就是**顺序请求**。

1.在一个请求完之后在顺序执行下一个请求，定义方法如下：

```javascript
//顺序处理
sequenceTasks(tasks) {
    function recordValue(results, value) {
        results.push(value);
        return results;
    }
    var pushValue = recordValue.bind(null, []);
    return tasks.reduce(function (promise, task) {
        return promise.then(task).then(pushValue);
    }, Promise.resolve());
}
```

2.接着定义上传图片的方法，把每个上传图片都变成**promise形式**:

```javascript
//图片promise
uploadImage(file) {
    return function() {
        return new Promise((resolve, reject) => {
            wx.uploadFile({
                //图片服务器
                url: uploadFileServerApi,
                filePath: file,
                success (res) {
                    resolve({
                        name: res.name,
                        path: res.path
                    });
                },
                fail (err) {
                    reject(err)
                }
            })
        });
    }
}
```

3.在需要上传图片的地方添加如下代码，

```javascript
// 图片上传方法
let promiseArr = [];
//把每个图片path编程promise形式
for (let i = 0; i < len; i++) {
    let promise = this.uploadImage(imageList[i]);
    promiseArr.push(promise)
}
sequenceTasks(promiseArr).then((result)=> {
   //处理result
})
```

### 三、bindInput卡顿
在小程序里面有bindInput方法，在输入框内容变化触发，一般在一边会定义方法把输入的文字放到data里面：

```javascript
bindInput(e){
    this.setData({ textContent: e.detail.value })
}
```
小程序里面**频繁setData**就会造成卡顿，使用**bindBlur**在**失去焦点的时候再去setData**，就不需要频繁去setData造成卡顿。


### 四、"invokeWebviewMethod 数据传输长度为 xxx 已经超过最大长度 1048576"问题解决
上拉加载数据，一直往data里面塞数据，最后就会报错。

```javascript
data:{
    list: []
}
```

可以利用子集list里面包含很多个子数组

```javascript
function safeRender(res) {
    let d = {}
    let l = `list[${pageIndex}]`
    d[l] = res
    this.setData(d)
}
```

wxml如下：

```html
<view wx:for="{{list}}" wx:for-index="index">
  <view class="content" wx:for="{{list[index]}}"></view>
</view>
```


### 五、textarea原生实现，总是浮动在最上层
解决方法有两个
1.直接**暴力隐藏textarea**，这个比较简单，但是用户体验可能比较差
2.使用**替代元素**，一个用于**用户输入**，一个**用于展示**，代码如下：

```javascript
<!-- 用户输入的textarea组件 -->
<textarea id="text-area" value="{{txtRealContent}}" bindinput='txtInput' wx:if="{{!showMask}}" />
<!-- 用于展示的textarea组件 -->
<view class='rich-text' style="{{('height:' + txtHeight + 'px')}}" wx:else>
  <rich-text nodes="{{txtRealContent}}"></rich-text>
</view>
```

换行符需要特殊处理:

```javascript
textareaContent.replace(/\n/g, '<br/>')
```

### 六、canvas原生实现，总是浮动在最上层
在做项目的过程中遇到了使用使用canvas画二维码的时候，二维码画出来之后，滑动的时候会**浮动在上层**，解决办法如下：
1.data里面定义二维码路径

```javascript
data: {
    QRImgUrl: '',//二维码图片路径
}
```

2.添加一个将二维码导出生成图片，并返回文件路径的方法：

```javascript
saveCanvas() {
  wx.canvasToTempFilePath({
    canvasId: 'qr-code',
    success: (res) => {
      this.setData({
        QRImgUrl: res.tempFilePath
      })
    }
  })
}
```

3.最后在调用draw之后（即绘制画布的方法），调用saveCanvas：

```javascript
setTimeout(() => {
    this.saveCanvas()
}, 1000);
```

**注：**这里的**setTimeout一定要加**，**如果画布没绘制完**，**图片路径导出来就是空的**。
经过手头的测试机测试，ios和模拟器在1000ms的情况下都可以显示，**部分安卓机3000ms才显示**，部分安卓机画了3000ms也显示不出来。。。
推荐使用`wx.createCanvasContext(canvasId, this)`，这个的draw方法是带有回调函数的，这样也不用setTimeout了，直接在回调成功的情况下，再调用`wx.canvasToTempFilePath`就可以了。


### 参考：
https://www.kancloud.cn/kancloud/promises-book/44249

https://developers.weixin.qq.com/blogdetail?action=get_post_info&docid=000222e78f0d38e3552636d5056804&token=16804411&lang=zh_CN

https://developers.weixin.qq.com/blogdetail?action=get_post_info&docid=0008ec49d849681c05866d9a957008&highline=input%20setData

https://juejin.im/post/5b6ab1f951882539766ea558
