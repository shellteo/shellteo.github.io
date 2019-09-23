---
layout: post
title:  "无广告互联网与网络货币化（Web Monetization）"
date:   2019-09-23
categories: 动如参与商
author: 张翔
location: ShangHai, China
description: "早在2004年，w3c组织的标准里就加入了触控（touch）事件的草案。从现在看来，显然有一种幸存者偏差，但是在15年前，能预测到触屏智能手机的兴起的，显示是少数。而且大多是技术和标准的先行者。2004年到现在15年间智能手机的故事太多，除了硬件方面的疯狂推进，外卖应用、直播、共享经济、短视频、社交电商等软件应用轮番登场也在改变着我们的生活方式。现在相比于15年前，文字语音图片视频等信息呈现几何倍数的爆炸，负面正面、真真假假、撕逼口嗨都让人失去理智，如何从无穷的信息中预测15年后的今天呢？或者说一部分的未来？也许能从w3c标准里找到答案。"
---
---
## 预测未来：从w3c开始
早在2004年，**w3c组织的标准里就加入了触控（touch）事件的草案**。从现在看来，显然有一种幸存者偏差，但是在15年前，能预测到触屏智能手机的兴起的，显示是少数。而且大多是**技术和标准的先行者**。2004年到现在15年间智能手机的故事太多，除了硬件方面的疯狂推进，外卖应用、直播、共享经济、短视频、社交电商等软件应用轮番登场也在改变着我们的生活方式。
![w3c 2004](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20190921/0.jpg)
现在相比于15年前，文字语音图片视频等信息呈现几何倍数的爆炸，负面正面、真真假假、撕逼口嗨都让人失去理智，如何从无穷的信息中预测15年后的今天呢？或者说**一部分的未来**？也许能**从w3c标准里找到答案**。

## Web Monetization

[Web Monetization草案](https://github.com/adrianhopebailie/web-monetization)，解释来说就是网络货币化，旨在为内容网站提供小额支付。

很多团队和项目进行互联网从信息互联到价值互联的探索。不管是区块链和数字货币的发展，标准化规范化都是必然的过程。上周在上海的区块链周投资者论坛讨论中Rin说到三个赛道，协议层的探索都显得尤其重要。


## 无广告互联网

上面提到的Web Monetization草案其实就是探索一种新的网络经济模型，去替代现在的互联网广告模型。实现用户和内容生产者一种良性的循环。并且区块链等技术的兴起让这件事进展更快也变得更加有必要。

今年二月份Mozilla组织的文章[探索网络的其他供资模型](https://blog.mozilla.org/futurereleases/2019/02/25/exploring-alternative-funding-models-for-the-web/)，提到了数字广告的生态大部分收益都流向了少数几家公司，并且存在着个人隐私的泄露。所以我们需要寻求一种更好的模式去平衡网络生态。

**7月份，Mozilla和Scroll合作，[推出5美元/月服务，提供无广告互联网体验](https://new.qq.com/omn/20190705/20190705A0RNLQ00.html)。**

![mo](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20190921/4.jpg)

google推出的[google contributor](https://contributor.google.com/v/beta)在这方面也有尝试。
![gc](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20190921/1.png)

所以这件事变得越来越急迫和重要。

**2019年09月17日Mozilla和微支付创业公司 Coil 宣布了一亿美元的[Grant for the Web](https://www.grantfortheweb.org)计划。**

## Grant the web计划
![Grant-for-the-Web](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20190921/3.png?x-oss-process=image/resize,h_200)

Grant the web计划的具体内容：
- 五年内，向为生态做出贡献的个人、组织、项目支付1亿美金
- 为新思想和协作的繁荣发展创造空间
- 委员会最初由Coil、Mozilla和Creative Commons的代表组成
- 增加用户的自主权、隐私权和对自己数据的控制权；促进互联网的多样性和包容性；
- 50％奖励给公开许可的软件项目和内容项目

正如[coil](https://coil.com)的CEO Stefan Thomas说的，“我们能回到一个地方，在那里，小人物也能真正在网络上取得成功。”

> That's one of the big dreams that we have around GrantForTheWeb, 
> we can get back to a place where the small guy really has a chance to make it on the web

关于这个计划的讨论也有很多，也不乏真正想要去做一些事情的。[cinnamon](https://www.cinnamon.video)就是其中之一。


## 网络货币化使用探索

对于Web Monetization标准，coil有一套[简洁的文档](https://coil.com/docs)可以使用。

#### 插入Meta标签
先安装chrome或者firefox插件，插入meta标签就可以使用了
```html
<meta name="monetization" content="$coil.xrptipbot.com/t027ZPQqQJqWA3ixOEPYbQ">
```

#### JavaScript API
可以根据document.monetization判断用户的状态
```javascript
//  没有 Web Monetization
document.monetization === undefined
// 等待中
document.monetization && document.monetization.state === 'pending'
// 开始支付
document.monetization && document.monetization.state === 'started'
```

#### 浏览器事件
可以通过监听monetizationstart事件来知道Web Monetization是否启动，monetizationprogress事件可以知道付款状态
```javascript
document.monetization.addEventListener("monetizationstart", event => {
    // 业务逻辑
})

document.monetization.addEventListener("monetizationprogress", event => {
    // 业务逻辑
})
```
npm包[react-web-monetization](https://www.npmjs.com/package/react-web-monetization)可以使用。

> 关于Web Monetization草案更多的[解释](https://github.com/adrianhopebailie/web-monetization/blob/master/explainer.md)，具体规范在[Interledger RFC 28](https://interledger.org/rfcs/0028-web-monetization/)



## 参考

[Mozilla推5美元/月服务，提供无广告互联网体验](https://new.qq.com/omn/20190705/20190705A0RNLQ00.html)

[Mozilla和Scroll合作测试网络的替代资金模型](http://www.wper.com/IT/201906/445.html)

[Mozilla partners with news subscription service Scroll to build an ad-free internet](https://www.theverge.com/2019/2/26/18241183/news-subscription-service-scroll-partner-mozilla-firefox-ad-free-internet)

[Exploring alternative funding models for the web](https://blog.mozilla.org/futurereleases/2019/02/25/exploring-alternative-funding-models-for-the-web/)

[Mozilla and Creative Commons want to reimagine the internet without ads, and they have $100M to do it](https://www.fastcompany.com/90403645/mozilla-and-creative-commons-want-to-reimagine-the-internet-without-ads-and-they-have-100m-to-do-it)

[A $100 Million Investment to Reshape the Economics of the Web](https://foundation.mozilla.org/en/blog/100-million-investment-reshape-economics-web/)

[Exploring Web Monetization](https://sallylait.com/blog/2019/09/18/exploring-web-monetization/)

[A $100 Million Investment to Reshape the Economics of the Web](https://foundation.mozilla.org/en/blog/100-million-investment-reshape-economics-web/)
