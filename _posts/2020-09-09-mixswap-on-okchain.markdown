---
layout: post
title:  "mixswap：基于OKExChain的uniswap平台【okchain黑客松三等奖】"
date:   2020-09-09
categories: blockchain
author: zx
location: ShangHai, China
cover: "https://mixswap.oss-cn-hangzhou.aliyuncs.com/1.jpg"
description: ""
---
---

前言：okchain黑客松已经结束了，这个项目取得了第三名的成绩。
- [链闻报道](https://www.chainnews.com/articles/010591306357.htm)
- [OKEX文章](https://www.okex.com/academy/zh/the-okexchain-hackathon-judges-are-out-see-who-gets-the-top-three)

因为做的时候OKExChain的API并不完善，只有CLI比较完整且问题比较少，所以我这边通过Nodejs通过CLI调用返回数据再解析，实现了一套简单的Nodejs API。而且当时OKExChain也不支持合约，所以这边通过后端也模拟了一套Uniswap的实现。支持了创建交易对、添加流动性、交易的功能。

## OKExChain介绍
OKExChain是基于cosmossdk的OKExChain的实现。
- [github](https://github.com/okex/okexchain)
- [文档](https://okexchain-docs.readthedocs.io/en/latest/index.html)
- [okb测试币领取](https://www.okex.com/drawdex)

## UniSwap介绍
Uniswap 是以太坊区块链上的一个去中心化交易平台，主要为ETH 和ERC-20 代币提供流动性服务，是目前用户数量最多的DeFi 应用。 Uniswap 最大的特点是自动化做市商（Automated Market Maker，简称AMM），用既定算法代替人工报价。


## demo video
[https://www.youtube.com/watch?v=YmpsitNKFqs&feature=youtu.be](https://www.youtube.com/watch?v=YmpsitNKFqs&feature=youtu.be)

## snapshot
![swap](https://mixswap.oss-cn-hangzhou.aliyuncs.com/swap.jpg)
![addLiquidity](https://mixswap.oss-cn-hangzhou.aliyuncs.com/addLiquidity.jpg)
![4](https://mixswap.oss-cn-hangzhou.aliyuncs.com/4.jpg)
![5](https://mixswap.oss-cn-hangzhou.aliyuncs.com/5.jpg)
![6](https://mixswap.oss-cn-hangzhou.aliyuncs.com/6.jpg)
![7](https://mixswap.oss-cn-hangzhou.aliyuncs.com/7.jpg)
![8](https://mixswap.oss-cn-hangzhou.aliyuncs.com/8.jpg)

## Sign up And Sign In
You can create a account using this system and sign in!

![sign up](https://mixswap.oss-cn-hangzhou.aliyuncs.com/2.jpg)
![mnemonic](https://mixswap.oss-cn-hangzhou.aliyuncs.com/mnemonic.jpg)
![sign in](https://mixswap.oss-cn-hangzhou.aliyuncs.com/3.jpg)



