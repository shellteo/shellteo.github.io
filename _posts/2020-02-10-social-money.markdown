---
layout: post
title:  "Social Money分析研究"
date:   2020-02-28
categories: 动如参与商
author: 张翔
location: ShangHai, China
cover: "https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200228/1.png"
description: "web2.0大多企业都在做平台，但是web3.0想将社区的价值与它们所存在的平台脱钩，从封闭平台到开放社区，实现跨平台的网络价值。用户可以通过网络社区的social money来加入、订阅、关注，增长和交换独立于平台的网络的价值。"
---
---

<iframe src="//player.bilibili.com/player.html?aid=92353922&cid=157680441&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>


## Introduction

![1](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200228/1.png)
<center>@dao + social money = $dao</center>


> web2.0大多企业都在做平台，但是web3.0想将社区的价值与它们所存在的平台脱钩，从封闭平台到开放社区，实现跨平台的网络价值。用户可以通过网络社区的social money来加入、订阅、关注，增长和交换独立于平台的网络的价值。

## 与DEX的相似处
越往后想就越觉得我们在向DEX靠拢
* 用户系统主要是以太坊账号
* 交易撮合系统交给**uniswap**
* 发行粉丝币相当于上币操作
* token staking可以对应于资金管理系统

大部分都是依赖于生态和去中心化的工具，行情或者说social money上榜服务是中心化的，可以作为公司的变现操作。

- 账号系统：以太坊账号 钱包/插件登录
- 交易撮合系统：uniswap协议，rmb to dai可以参考[webotc](https://www.whaleex.com/webotc)和[moonpay](https://buy.moonpay.io/)
- 上币系统：social money发行
- 资金管理系统：token质押
- API/SDK 设计参考[1inch.exchange](https://1inch.exchange/#/api)、[WhaleEx](https://github.com/WhaleEx/API)、[binanceDEX](https://docs.binance.org/)，其实主要还是uniswap的API和SDK，我们提供信息API和rmbToDai

## 长尾市场

> 长尾市场是指那些原来不受到重视的销量小但种类多的产品或服务由于总量巨大，累积起来的总收益超过主流产品的现象。比如豆瓣当年的增长，离不开长尾效应。


![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200228/2.gif)



我们再来看一下uniswap协议，最主要的是`x*y = z`，其实就是反比例函数，再坐标轴上画出的图形就是和**长尾市场的图形完全一致**。其实也侧面反映了uniswap主要应用场景，以及我们social money为什么选择uniswap。

![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200228/4.png)

长尾市场的本质：它们看似很小，微不足道，但都能够积少成多，聚沙成塔。从这个角度上说，**长尾的革新并不是在于发现了一个新市场，而在于通过营销方式的变革来达到长尾。** 根据最适宜刺激水平理论，每个顾客或客户都有一个特定的、相对稳定的自我感觉舒适的刺激点，该点就是顾客或客户的最适宜刺激水平。


## 营销和运营

- 定位：垂直领域的DEX
- 核心：人人发币，扩大Token的应用范围
所以总结来说，我们应该做的是**垂直领域的DEX**，是一个人人发币的设计，和DAO模式也是非常契合的。
如果说社区是一辆车，那么social money就是里面的核心燃料。

## 技术架构图（WIP）
![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200228/4.png)

- 用强类型的 TypeScript 语言作为后端开发语言，抛弃 js
- 后端框架选用 [Nest.js](https://nestjs.com/)
- 使用uniswap作为交易系统（我们直接使用uniswap协议，只需要魔改前端，加入我们的粉丝币）

## 我们需要做的是事情

1. 找到用户的尖叫时刻，之前产品发展上的几次重要突破引入粉丝币，然后添加了微信支付，尖叫时刻指的是你的产品和服务令用户眼前一亮。
2. 产品的功能点原型图（link，zx，小田）
3. 根据原型图出设计（胡晨星）
4. social money后端开发（zx，frank, etc...）
5. uniswap这部分需要有人去研究和调研
6. 瞬matataki和tg机器人的后续修改


## 参考
[Niche markets most likely driver of DEX adoption](https://tonysheng.substack.com/p/niche-markets-most-likely-driver)

[长尾市场是DEX的未来吗？](https://orange.xyz/p/469)

[Front Running：去中心化交易的阿喀琉斯之踵](https://www.chainnews.com/articles/285280126963.htm)

[DAO和social money以及瞬的发展](https://shellteo.top/social-money-dao/)
