---
layout: post
title:  "🐝Bee2 Finance: 采用二次方机制的DeFi实验"
date:   2020-09-06
categories: bee2
author: zx
location: ShangHai, China
cover: "https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/cover.png"
description: "DeFi不应该成为巨鲸的游戏，而是需要成为所有人的金融。Bee团队通过二次方机制来实现这一点。"
---
---
🔗 原文链接：[https://medium.com/@bee2.finance/bee2-finance-an-experiment-using-quadratic-mining-d4bcced702e2](https://medium.com/@bee2.finance/bee2-finance-an-experiment-using-quadratic-mining-d4bcced702e2)

翻译校对：zx

DeFi不应该成为巨鲸的游戏，而是需要成为所有人的金融 。

## 为什么取名为Bee2
🌷 + 🐝 = 🍠

农田耕作需要蜜蜂

2代表二次方机制

我们都是蜜蜂，在DeFi的世界中，我们建造，我们改变！

## 背景
BEE2这个项目脱胎于[《激进市场》](http://radicalmarkets.com/)这本书，书的第二章「Radical Democracy」引进了一种创新的投票方式 QV （二次方投票）来改进集体决策的效率和制衡。

QV这个机制这几年也迅速流行了起来，最早的实践在[台湾的hackathon](https://presidential-hackathon.taiwan.gov.tw/en/)活动上，他们通过投票选出获胜的项目。

QV的一个衍生是Quadratic Funding (QF)，最近[Gitcoin Grants](https://gitcoin.co/grants/)也在项目融资里面也用到了QF机制。

Vitalik本人对于二次方机制也是非常推崇，他的文章[Quadratic Payments: A Primer](https://vitalik.ca/general/2019/12/07/quadratic.html)就主要介绍二次方机制在支付领域的使用。

我们团队认为DeFi的流动性挖矿正在掉入马太效应陷阱，它让富人更富，穷人更穷，小玩家获得的收益都不够支付以太坊上高额的gas费。为此我们发起了bee2.finance这个项目，一个基于二次方挖矿机制的DeFi项目。
![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/2.png)

## 如何工作
二次方机制能被用在投票、支付、基金等领域。而且很多都获得了不错的反响和效果。
所以基于二次方机制，把它和现有的流动性挖矿机制进行结合。我们把它称为二次方挖矿（QM）。

举个例子，Alice 和 Bob分别同时投入$10000和$100去挖矿，在线性挖矿的下面，Alice收益的是Bob的100倍，但是在二次方挖矿，Alice收益是Bob的10倍。

相比较当前DeFi流动性的挖矿项目，收益和成本是线性增长的，二次方挖矿收益和成本是指数增长的（二次方），如下图1。
![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/3.png)


图2比较了在线性和二次方挖矿下的成本和收益。

![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/4.png)

图3计算了想要获得更多收入的边际成本。想要获得更多收益，就需要付出更多。由于挖矿成本是二次方的，因此影响会受到抑制：拥有 100 倍以上资金的人，只有约 10 倍的影响力，而不是 100 倍，因此，问题的严重程度会下降 90%（对于超级富有者而言，这种削弱会更强）。也就是说，进一步缓解这种权力不平等，可能是可取的。


![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/5.png)

## 启动
我们希望能有更多人参与进来，让大家都能获得收益。让流动性挖矿这件事变得更简单。我们设计了两个token，BEE 和 HONEY，BEE是我们的主要Token，HONEY是给用户的额外奖励。

### $BEE🐝
BEE发行量为500万。为了把BEE分发给更多的人，我们把初始流动性池子定为下面四个，每个池子分配25万个BEE。

- [wETH](https://etherscan.io/token/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2)
- [LINK](https://etherscan.io/token/0x514910771af9ca656af840dff83e8264ecf986ca)
- [ANT](https://etherscan.io/token/0x960b236A07cf122663c4303350609A66A7B288C0)
- [YAMv2](https://etherscan.io/token/0xaba8cac6866b83ae4eec97dd07ed254282f6ad8a)

使用YAMv2的池子挖矿主要是因为yam的社区很棒，社区很团结，[save yam](https://medium.com/@yamfinance/save-yam-245598d81cec)也很成功。

选择ANT的原因是DAO很棒，我们希望DAO社区能越做越好，希望后续能和[aragon组织](https://aragon.org/)合作，去实现更加公平的世界，更加去中心化的世界。

初始分配池启动之后，后续流动性池的开启会根据投票决定。

我们希望Bee2能长期发展下去，为此我们设立了开发基金，10%的BEE被用来开发、未来迭代、还有合约审计。并且我们还希望我们后续会参与DAO的开发，希望带给大家更多DeFi的创新的产品。

### $HONEY🍯 
🐝  + 🍠 = 🍯 
HONEY是由用户产生，总量不固定，由用户在池子里面的BEE数量决定（已提取的BEE数量不影响之前已经生成的HONEY数量），如下图

![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/6.png)

每个池子都能产生HONEY，但是需要满足下面两个条件：

1. 周期内池子质押Token数量的最大值大于我们设定的固定值
初始四个池子固定值分别是：10,000 WETH, 500,000 LINK, 500,000 YAMv2, 1,000,000 ANT.
2. 池子挖矿时间超过两天，也就是挖出BEE的数量大于69120

我们不对honey做其他任何机制设计，它没有任何价值，完全属于用户。我们遵循YFI的精神：没有团队份额，没有预挖，没有公募。并且它在多次二次方机制之后，它会变得足够分散，巨鲸效应会被进一步削弱。


## 合约
BEE address: [0xcD4292701995F4707AE63fb1a48d80dB2c5F04D4](https://etherscan.io/token/0xcd4292701995f4707ae63fb1a48d80db2c5f04d4)

HONEY address: [0x932441a0F11482CB5f8B8900aB981a39806F88fa](https://etherscan.io/token/0x932441a0F11482CB5f8B8900aB981a39806F88fa)

Pool address:
- BEEETHPool: [0x3E63e6F0D6E90E120Eb31e005aa149b476a89492](https://etherscan.io/address/0x3e63e6f0d6e90e120eb31e005aa149b476a89492)
- BEELINKPool: [0x633992502d7c13E8f95A8B8E44f17BA17EB2Db6D](https://etherscan.io/address/0x633992502d7c13E8f95A8B8E44f17BA17EB2Db6D)
- BEEYAMv2Pool: [0x519b240A5b834A6012400Cb955AE6dc632653dDD](https://etherscan.io/address/0x519b240A5b834A6012400Cb955AE6dc632653dDD)
- BEEANTPool: [0x9bD2e195c1d8D4023103067f112894f85306CF43](https://etherscan.io/address/0x9bD2e195c1d8D4023103067f112894f85306CF43)

合约未审计。在现有的流动挖矿代码上，我们修改的不多，并且我们做了很多测试，下一阶段我们会注重代码的审计，更加确保安全。

## 治理
BEE也是我们的治理代币，我们投票需要使用BEE。

![](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20200906/7.png)

我们会在我们的治理系统中也继续使用QV。再结合我们的二次方挖矿，两次二次方机制让投票变得更加公平，考虑到以太坊上比较高的gas费，分地址的女巫攻击可能性会比较低。

## 下一步计划
二次方挖矿背后其实反映的是自由激进主义的思想。自由激进主义是指以群体意志为主导，它是相对于自由个人主义和自由民主主义的一个中间状态。

但是二次方机制并不是完美的解决方案，只是提高了人们作弊的成本，相应问题的还会有女巫攻击和共谋问题，这需要我们通过更加合理的机制去解决。

后续我们会和将二次方做成一个更广泛的协议（Quadratic Protocol），让更多人能享受去中心化金融带来的好处。

我们团队会继续朝着这个方向努力，用区块链让世界变得更好，更公平。而且我们会把公平，激进践行到底，帮助更多的人。

# 官方资源
- 网站: [https://bee2.finance](https://bee2.finance)
- Twitter: [https://twitter.com/bee2_finance](https://twitter.com/bee2_finance)
- Telegram: [https://t.me/bee2_finance](https://t.me/bee2_finance)
- Discord: [https://discord.gg/CuDP4jK](https://discord.gg/CuDP4jK)