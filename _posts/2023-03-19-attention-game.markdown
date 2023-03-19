---
layout: post
title:  "The Attention Game"
date:   2023-03-19
categories: project
author: zx
location: ShangHai, China
cover: "https://zxblog.oss-cn-hangzhou.aliyuncs.com/20230319/hexbin.jpeg"
description: "Attention game是一个链上的NFT社交游戏实验。并且赋予了NFT一些功能属性(Show as a Service Or Ads as a Service)"
---
---

## One Sentence
Attention game是一个链上的NFT社交游戏实验。并且赋予了NFT一些功能属性(Show as a Service Or Ads as a Service)

## Background
1. 游戏灵感来源于Status as a Service（下文称SaaS），来自于Eugene Wei 2019年的文章[status-as-a-service](https://www.eugenewei.com/blog/2019/2/19/)，解析了社交网络的发展模式，每个人都是追求地位的猴子。SaaS更侧重于社交媒体平台上的虚拟社交经济。社交网络本质上是一种ICO行为。社交网络本质上是一个展示用户身份和社会地位的平台。设计的点赞和转发的功能也是为这一特点服务。

2. 所以我们就在想如果将这些内容转变为一个链上的游戏实验，不管是去年，[《去中心化社会：寻找 Web3 的灵魂》](https://web3caff.com/zh/archives/10440)v神这篇文章，都在寻找Web3到底往何处发展，并且提出了Soul Bound Token这个概念，想建立一个更丰饶、多元化的生态系统称为 “去中心化社会”（DeSoc）——  一种共决共定的社交关系。

3. 除了社交网络的本质之外，2022年NFT的发展非常快，一些新的NFT发行方式也吸引了我们的注意，不管是Nouns的每日拍卖模式，还是Art Gobblers设计的VRGDA（Variable Rate Gradual Dutch Auctions）的NFT发行模型，都更加从供求关系去思考NFT的供应模型，并且将token和NFT的发行做了结合。

4. 我们设计还参考了[六度分隔理论](https://en.wikipedia.org/wiki/Six_degrees_of_separation)，这个理论概括就是认为世界上任何互不相识的两人，只需要很少的中间人就能够建立起联系。

## What is This Game Like?(the Game Mode)
基于上面背景我们设计一个可以发行NFT并且可以抵押NFT的游戏。

* 每个NFT都是一个六边形，游戏由这些六边形NFT组成（下面称为HexNFT)

* 游戏创世之初只有一个HexNFT，我们定为坐标(0,0)

* 后续想mint HexNFT的发行只能跟已有HexNFT相邻（Link Mint）

* HexNFT的总量为10,000个

![hexbin3d](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20230319/hexbin3d.jpeg)

* HexNFT本身都是空白的，可以质押NFT（Ads As a Service），Hex-NFT就可以展示这些质押的NFT

* 这些HexNFT本身可以通过marketplace买卖

* 每个HexNFT都会产出代币，并且根据距离(0,0)的距离，产出代币的数量会有递减，下面会有介绍

## Token Economics(TBD)
游戏内会发行自己的代币——ATT，作为游戏的治理代币
每天产出的代币数量固定，质押NFT需要消耗ATT代币
...more...

## What’s Next

1. 为每个HexNFT添加链接到twitter等web2社交的功能，也可以添加链接到link3/Lens的功能，以增强社交属性，类似于一种社交图谱。

2. 质押的NFT使用户的头像，每个HexNFT都是他们的账户空间，可以发布自己liked NFT list。
