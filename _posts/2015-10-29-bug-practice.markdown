---
layout: post
title:  "Bug实战"
date:   2015-10-29
categories: Other
author: 张翔
location: NanChang, China
cover: ""
description: Bug实战
---
---
#### 问题1
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/1.png)

错误代码：
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/2.png)

正确代码：
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/3.png)

错误：
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/4.png)

解决：启动了两个HTTP服务器，都使用的3000端口，关掉一个就好了

#### 问题2
next( )很重要！不要忘记了
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/5.png)

#### 问题3
错误：忘记开数据库的bugs！

#### 问题4
错误：Error: Can't set headers after they are sent.怎么解决？
解决：“Can’t set headers after they are sent.” => “不能发送headers因为已经发送过一次了” => 在处理HTTP请求时，服务器会先输出响应头，然后再输出主体内容，而一旦输出过一次响应头（比如执行过 res.writeHead() 或 res.write() 或 res.end()），你再尝试通过 res.setHeader() 或 res.writeHead() 来设置响应头时（有些方法比如 res.redirect() 会调用 res.writeHead()），就会报这个错误。

（说明：express中的 res.header() 相当于 res.writeHead() ，res.send() 相当于 res.write() ）

原因就是你程序有问题，重复作出响应，具体原因很多，需要自己根据以上的原则来排除。
express可能是有两个res.render('shouye');

#### 问题5
错误：
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version
```javascript
var delete_sql = 'DELETE FROM example WHERE id=?';
```
解决：在语句前加入表名

```javascript
var delete_sql = 'DELETE FROM example WHERE example.id=?';
```

#### 问题6
错误：
getExampleData failed:Error: Cannot enqueue Query after fatal error.
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/nodejs-practice/bug/6.png)

把数据呈现在页面上

#### 问题7
错误：
req.body为{}，或者req.body.parmars为undefined的情况
解决：
1.

npm install body-parser --save
and then do this in your code
```javascript
var bodyParser = require('body-parser')var app = express()
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())
```

2.
在模板页面上
设置enctype="multipart/form-data"；
所以：Content-Type:multipart/form-data; boundary=----WebKitFormBoundarywOVhYz4PVAWmA4V3
如果想要正确显示req.body 则设置Content-Type: application/x-www-form-urlencoded

3.
使用插件：multer或者multiparty

