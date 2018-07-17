---
layout: post
title:  "基于Mock.js的前后端分离思考与实践"
date:   2017-08-11
categories: Mock.js
author: 张翔
location: ShangHai, China
description: 基于Mock.js的Mock server怎么搭建，怎么选择好用的API接口文档编写API文档
---
---
&emsp;&emsp;最近在写小程序，前后端分离，后端接口给得很慢，就算给出来了，各种接口调不通，到时候前端给后端接口还需要各种调试，各种修改，总之费心费力，事情好像就这么恶性循环下去。我们需要在后端还没写好接口的时候，前端需要构造假数据以防止自己的开发被后端阻塞。Mock.js就有这样的作用，根据官网Mock.js的特点有：前后端分离、增加单元测试的真实性、开发无侵入、数据类型丰富、方便扩展。总而言之，基于Mock.js前端工程师就能独立于后端开发了。

&emsp;&emsp;如何进行数据模拟，构建mock server呢？因为小程序不存在跨域问题，所以这里的mock server只需要解决路由功能即可。

## 1.模拟假数据生成API的工具有json-server
根据官网介绍一个假的REST API只不需要不到30秒的时间。
你只需要一个json文件，文件里面是你想要的数据，然后安装json-server，运行
```javascirpt
$ json-server --watch db.json
```

你就能通过http://localhost:3000/posts/1访问到你的数据。
但是这个工具API自由度不高，你只能通过你的json文件里面的数据来获取API数据，不能像Mock.js那样自由度高的定制API，但是他的特点就是方便快速，你只需要写一个json文件，其他`POST`、`PUT`、`PATCH`和`DELETE`请求会自动保存到你创建的json文件里。而且他还提供如下特性：
过滤GET `/posts?title=json-server&author=typicode`
分页GET `/posts?_page=7&_limit=20`
排序GET `/posts?_sort=views&_order=asc`
数据分片GET `/posts/1/comments?_start=20&_limit=10`
搜索GET `/posts?q=internet`
等一系列特性
POST，PUT或PATCH请求应包含Content-Type：application/json请求头部。
```javascript
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

## 2.利用Mock.js来模拟API
首先Mock.js只是生成假数据的工具，不具备路由功能，不能快速启动服务器，这个必须要自己集成。但是Mock生成假数据更加自由和可配置。
Mock.js的API这里就不多言，官网提供了文档和实例很详细。详情见`http://mockjs.com/`
Mock文档：`https://github.com/nuysoft/Mock/wiki/Syntax-Specification`
这里介绍怎么搭建一个集成Mock.js的服务器Mock-server-master
首先创建也一个API文件夹
创建`getjson.js`文件如下
```javascript
export default {
  '/user/1': () => ({
      "id": 1
  })
}
```

再创建`postjson.js`文件,如下
```javascript
// post router
export default {
	'get /postjson': () => ({
		"post|1-10": [{
			title: "@title"
		}]
	}),
	'get /postjson/:id': param => ({
		id: param.id,
		title: "@title",
		content: "@paragraph"
	})
}
```

postjson文件和getjson文件都是给提供mockjs的数据和规则的，以及标识get或者post方法的。接下来我们需要创建服务器，新建server.js,按如下步骤，首先引入mockjs,用于解析json.js文件的mock规则，
然后引入ohana，是一个返回json数据的http服务器
include-all模块能够文件夹获取文件夹里的文件和文件内容，以json对象的形式返回
```javascript
import Mock from 'mockjs';
import Server from 'ohana';
import include from 'include-all';
import path from 'path';

//include-all模块能够根据文件夹获取文件夹里的文件和文件返回内容，以json对象的形式返回
const files = include({
  dirname: path.resolve(__dirname, 'api')
})
//files是一个对象，循环对象
const composePaths = files => {
  const obj = {};
  for(let filename in files){
      if(files[filename] && files[filename].default){
        const item = files[filename].default;
        for(let key in item){
            obj[key] = item[key]
        }
      }
  }
  return obj;
}

//可以通过定义post方法或者get方法
const parseRoute = routes => {
  const paths = [];
  for(let key in routes){
      const k = key.split(' ');
      const item = {
        handler: routes[key]
      }
      if(k.length == 1){
        item.method = 'get';
        item.path = k[0]
      }else{
        item.method = k[0];
        item.path = k[1]
      }
      paths.push(item);
  }
  return paths;
}

const routes = parseRoute(composePaths(files))

// mock的使用
const server = new Server()
routes.forEach(item => {
  server[item.method](item.path, {
      data: () => {
        return Mock.mock(item.handler.apply(this, arguments));
      }
  })
})
//监听3000端口
server.start(3000)
```

安装`nodemon`，`nodemon`能够监听文件变化，然后自动重启服务器
运行如下命令:
```javascript
nodemon --require babel-register server.js
```

打开浏览器访问`http://localhost:3000/user/1`，可以获取获取到{"id":1}的数据,访问`http://localhost:3000/postjson`可以返回随机生成的数组。

## 3.API的书写以及测试工具swagger
&emsp;&emsp;Swagger是一个简单但功能强大的API表达工具。它具有地球上最大的API工具生态系统，数以千计的开发人员，使用几乎所有的现代编程语言，都在支持和使用Swagger。使用Swagger生成API，我们可以得到交互式文档，自动生成代码的SDK以及API的发现特性等。
&emsp;&emsp;swagger editor是API文档编辑器，基于yaml语法书写的，根据OpenAPI规范自动生成API文档，功能强大，它能够提供语法高亮、自动完成、即时预览等功能，具体请参照官方文档，这里只简单介绍。详情见`http://editor.swagger.io`
#### swagger ui是API文档生成以及测试的工具，方便快捷，优点如下
- 支持API自动生成同步的在线文档
- 这些文档可用于项目内部API审核
- 方便测试人员了解API
- 这些文档可作为客户产品文档的一部分进行发布
- 支持API规范生成代码，生成的客户端和服务器端骨架代码可以加速开发和测试速度

### 4.可视化API管理工具-RAP
&emsp;&emsp;RAP是一个可视化的WEB接口管理工具。在RAP中，您可定义接口的URL、请求&响应细节格式等等。通过分析这些数据，RAP提供MOCK服务、测试服务等自动化工具。RAP同时提供大量企业级功能，帮助企业和团队高效的工作。详情见`http://rapapi.org`
&emsp;&emsp;对于前端开发来说，我也根据需求来参与接口的制定，但是最重要使用Mock数据，这样可以让前后端并行开发，而且RAP自动生成mock数据的规则就是根据Mock.js来的，所以说，RAP生成的数据自由度高。

这是我这边的一个例子，可以看到这边可视化的填写请求参数和响应参数，RAP能够根据填写的响应参数自动生成Mock数据，生成规则根据Mock.js
规则，mock数据可以看到规则和数据，下面这个url ，post可以获取到数据。
![图片1](http://mmbiz.qpic.cn/mmbiz_jpg/C5rBNNe9HlUDGqxd5Jj12tInbyJ0FSicEmSLG7QQEDiaCxxlMsZnba4KrOiagja6hQaHJe7xn1ujGTic3nq05Licmdw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)
![图片2](http://mmbiz.qpic.cn/mmbiz_png/C5rBNNe9HlUDGqxd5Jj12tInbyJ0FSicEguWKEZqs8icjVc69Vdgv8lfn59QNkamHRam3IMoib8SJiayaibqiaT2yybw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
![图片3](http://mmbiz.qpic.cn/mmbiz_png/C5rBNNe9HlUDGqxd5Jj12tInbyJ0FSicEUfysWBWDDRvcZq4twiaGib76t1WLv8vLPpKUZcqZmy21b8IlRHiaIHumw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)