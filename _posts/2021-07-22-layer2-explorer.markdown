---
layout: post
title:  "分享-Layer2扩容探索"
date:   2021-07-22
categories: 翻译 
author: zx
location: ShangHai, China
cover: "https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/cover.png"
description: "分享关于Layer2的一些内容，包括状态通、Plasma、侧链、Rollup"
---
---

前言：给公司做的分享讲的是Layer2扩容的介绍，下面是ppt链接和讲解的视频

- [Layer2扩容探索-ppt](https://docs.google.com/presentation/d/1BV-BFgz556OdjiUtQvQiHL9vWmY6EJMU/edit?usp=sharing&ouid=113372584326754866424&rtpof=true&sd=true)

- [现场视频](https://drive.google.com/file/d/1RivJovy9oMr3yhO2N8FKqtk3H-CUxcLW/view?usp=sharing)


下面是正文内容

---

大家好，我叫Albert，今天给大家带来的分享是Layer2的探索和具体的实践。

其实之前公司内部之前就有人分享过layer2，当时分享介绍了polygon的matic代币。但polygon其实是layer2的解决方案中的一种。随着五六月的大跌，其实现在链上活动已经降低了很多，市场需求越来越弱，gas也降低了几十倍。所以好像在这个节点去讨论Layer2好像不是太必要，我的理解是，市场是周期性的，未来以太坊应该大概率还是会拥堵（以太坊2.0还比较遥远啊），也算是未雨绸缪和抛砖引玉， 而且最近也有很多layer2的一些项目获得了更多融资和关注，所以从这点出发，layer2的讨论是非常有必要的。

开始之前需要思考几个问题
1. 什么是layer2？
2. 为什么会有layer2？
3. 以及layer2分类有哪些？
4. layer2最近的进展如何？

## 从加密猫和Fomo3d讲起

加密猫很多人都有所耳闻，是一款链上游戏，用户可以购买、繁殖出售和手续虚拟猫咪，17年年底上线的以太坊主网，上线之后大火，也顺带阻塞了以太坊网络。这个游戏后面也有非常多的仿盘。

![加密猫](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/0-0.png)

![Fomo3d](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/0-1.png)


右边也是一款18年的链上游戏`Fomo3d`，这个游戏的规则是限定时间内用户用以太坊购买游戏内的key，这些购买的以太坊会进入奖池，时间结束后最后购买key的玩家，能够获得奖池中的大奖。第一轮的赢家利用以太坊本身的拥塞问题使得自己成为最后购买key的玩家，获得了超过 1 万以太的奖励。其实这也不是bug，只是因为以太坊吞吐量低，每秒只能处理十几笔交易。

上面说了两个游戏都跟以太坊拥堵有关，其实也不只有以太坊拥堵，比特币，其他公链也都会有这个性能问题，所以就需要去解决这个问题，也是我们今天需要讨论的，最容易想到就是增加区块的大小来扩容，像bch就是这么做的。会改变区块链底层分布式账本层的我们称为一层扩容。一层扩容其实还有分片，以太坊2.0要实现的分片，分片简单理解就是一个人的活分成多个人一起完成，用来提高效率。

比特币和以太坊是目前使用最为广泛的区块链平台，那号称世界计算机的以太坊，现在每秒却只能处理大约15笔交易，而现在大部分互联网平台的tps都是每秒几万甚至几十万的。所以这就很讽刺，在以太坊2.0还是一个遥远的故事的时候，就非常需要一个能够立马能用解决问题的方案。既保证了安全性和去中心化，也能提高可扩展性。

其实对区块链比较了解的同学应该是知道区块链的三难困境（The Blockchain Trilemma），也叫不可能三角。就是说，可扩展性、安全性和去中心化，三者只能得其二。有的公链其实是牺牲了部分去中戏化，去达到可扩展性。但是像我们熟悉的以太坊和比特币，就是选择了安全性和去中心化。所以相应的像以太坊和比特币就会有可扩展性的问题，也就是tps不够高。

![The Blockchain Trilemma](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/0.png)

区块大小扩容
- 分片：ETH2.0、Near、波卡、Nervos
- 其他：Flow的多角色架构区块链、Solana的工作历史证明（Proof of History）等等...

那怎么去解决这个问题呢？最容易想到的其实就是layer1的解决方案，像区块大小的扩容，比如比特币分叉bch就是支持大区块、还有像分片，分片也是eth2.0中实现可扩展性非常重要的解决方案，分片简单理解就是一个人的活分成多个人一起完成，用来提高效率。

>
>（本质上分片就是一个异步处理的机制，目前绝大多数分片都需要一个可信中介来协调处理，ETH2.0 里面是信标链，波卡是中继链，Elrond 是元数据链，唯一不需要中介的是 Near，这也是为什么 Near 号称「最强分片」。）
>

其实还有一些其他不主流的解决方案，比如Solana的工作历史证明`（Proof of History）`、`Flow`的多角色架构区块链…


> 多重角色架构说白了就是把流水线这个概念引入了节点。目前的公链都是几个节点一起打包一个网络，一起出块，每个节点的责任也一样，都是干着重复的活（假设没有分片的话）


那说了这么多，这些方案其实都需要改变区块链一层的工作模式或者系统架构来实现，所这种方式往往需要产生硬分叉来解决。所以代价会很高，因为还会有一些生态的迁移。
那么自然而然就会有人思考，能不能有一些不改变区块链的一层的链下解决方案来实现扩容。所以这就是layer2的产生。这也解释上面提出的两个问题：“什么是layer2？为什么需要layer2？”

说了这么多，那么layer2又分成那些解决方案呢？现在大部分人会把现在的layer2的方案分为4种，状态通道、plasma、侧链和rollup。这里面我们会主要介绍rollup这种解决方案，因为rollup可以算是现在的主流方案，最能率先落地，也是会最先被验证的方案，其他的几种方案，有的已经没有活力了没有人去研究了，像plasma方案。

- 状态通道（State Channel）
- Plasma
- 侧链
- Rollup

---

## 1. 状态通道

![state-channel](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/1-0.png)
那么最早被提出来的layer2解决方案就是状态通道，那比特币的闪电网络就是状态通道的具体实现的，在以太坊中对应的仿盘就是雷电网络，但是雷电网络其实完全就属于失败的产品，在以太坊所有的扩容方案中也是存在感最弱的，所以在以太坊上还有其他的团队去做状态通道这条路上的探索，比如Celer Network。

- 闪电网络（Lightning Network）、Raiden Network、Celer Network、Connext

我们可以就发展最好的闪电网络去解释一下，闪电网络是由Joseph Poon 和 Thaddeus Dryja 于 2015 年 2 月提出来的，也是最早被提出来的扩容方案。简单解释就是多个微支付通道连接而成的网络。主要核心的概念包含两个，一个是RSMC，这个很好理解，就是准备金机制，也就是交易双方都需要提前预存资金到微支付通道中，这样才能开启交易另外另外一个概念HTLC，中文翻译过来是哈希时间锁定合约，是保证通道内交易的安全的。


> 闪电网络双向支付通道只能实现通道通道内的安全交易。但是，不可能在闪电网络所有节点间都建立双向支付通道，那样就构成了一个全连接网络。因此，必须有一种方案来实现在没有建立双向支付通道的两个节点间的安全交易。该方案的核心就是HTLC。 
> HTLC的目的是在多个节点间通过HASH的方式来传递全局信息。其关键点有两个。一个是限时执行，只在一定时间内有效。第二个是基于时间的资源释放，这种资源释放是通过揭示前像（preimage）来实现的。 

- RSMC（Recoverable Sequence Maturity Contract）
- HTLC（Hashed Timelock Contract）


说了这么多，我们可以看一下闪电网络现在的数据，可以看到通道内质押的比特币数量大概是1797个，大概6千多万，这个可以算作是闪电网络的TVL，但是我们可以对比一下现在热门defi的TVL，大部分都是10亿以上，所以闪电网络还是有很多弊端的。

1. 使用闪电网络的前提是开通道和质押

2. 闪电网络的退出是有时间限制的，然后用户需要时刻关注通道内自己资金的安全，不然资金可能会受损失，其他后面也提出了暸望塔的解决方案。但是这一系列的问题也现在了闪电网络的大规模使用。

![lighting network](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/1.png)
（上图数据来自[https://1ml.com/statistics](https://1ml.com/statistics)），这里有可视化的网络图[https://explorer.acinq.co/](https://explorer.acinq.co/)


## 2. 侧链

![sidechain](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/2-0.png)

那我们接下来解释侧链的技术，其实我感觉侧链不算是一种layer2的解决方案，那么广义上来说侧链分为需要信任和不需要信任的。当前市场上有太多的项目方会自称是以太坊的 Layer 2，但本质上它们和以太坊网络是属于竞争关系，那真正的以太坊 Layer 2 是哪些？简单来说，它们是将计算和数据存储都迁移到⼦链上，而将结算交给以太坊主网处理的扩容网络，而前者与后者是属于共生的关系。需要信任的链，你跨链交易转账等，都是需要信任这条链是不会跑路。侧链又独立的分布式账本层

- 需要信任：xDai、bsc、heco、polygon等
- 不需要信任的

拿最近很火的游戏axie infinity来举例吧，Axie Infinity 是最近很火的数字宠物养成及战斗游戏。

观察到 Axies 单日成交量，在今年 5 月 1 日左右开始暴涨。这是由于 4 月 28 日前后，平台正式完成了技术升级，游戏从 ETH 整体迁移到了自研的侧链 Ronin 上（通过这一改变，玩游戏时不需要支付额外的 Gas 费，且任何收益都能实现秒到账）。

![axies](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/3.png)

对于游戏来说，数据全部上链，gas非常高，而且是非常没必要的，所以对于axie来说，迁移到自己的侧链上之后才实现了零gas和正真的数据自由。

而且axie的团队很早（大概是去年6月份，[写了想做侧链](https://medium.com/axie-infinity/introducing-ronin-axie-infinitys-ethereum-sidechain-8745e31eaef1)）就意识到了以太坊的局限性，开始开发Ronin侧链。
自 Axie Infinity 专属的以太坊侧链 Ronin 推出以来，Axies 一直呈现指数级的速度增长。上图清楚地展示了迅速俘获用户的场景（数据截止 6 月底）：自 4 月底以来，DAU、Axies 持有者、每月市场成交额和 Discord 成员都出现了显著增长。Axie 社区金库也取得显著增长。
由于 Ronin 的 Gas 费用与以太坊相比可以忽略不计，使得更多用户更容易进行繁殖，从而推动 Axie 数量、繁殖费用和市场成交额的增长。

## 3. Plasma

第三部分介绍Plasma

plasma出现的背景是在 2017 年 8 月，Ether 的价格接近历史新高。以太坊区块链使用量暴增，在不断增长的需求下以太坊不堪重负。研究者和开发者疯狂地寻找新的扩展性解决方案。

为什么说Plasma也属于侧链的一种，但是又不需要基于信任呢。和侧链不同的地方是，Plasma 将 Layer2 交易数据处理后提交到 Layer1（但是不是提交完整的数据，只是默克尔树根），并且增加了欺诈性证明的退出机制，通过这种方式利用 Layer1 算力保障 Layer2 安全性。

> Plasma 于 2017 年 8 月由 Joseph Poon 和 Vitalk Buterin 首次提出，Joseph Poon之前也是做闪电网络的哥们。


Plasma 设计模型有两个主要的分支：
1. Plasma MVP
2. Plasma Cash
3. 还有其他 Plasma，比如 Plasma Debit、Plasma Prime和其他可行的 Plasma

其中讨论比较多的就是前两个，我们这边也简单介绍一下MVP和Cash。MVP就是plasma的最小可用版本，**用户资产的安全主要依赖于用户及时发现恶意行为**，并退出子链。所以plasmaMVP最致命的就是批量退出的问题，因为网络依赖用户发现恶意行为并及时退出，这里面就会有运营商行为不当导致的退出。因为是MVP，所以会着重于快速实现，所以本身比较简单，会有很多问题，并不能用于实际生产。所以就产生了Plasma Cash，Plasma Cash其实是对Plasma MVP的改进。

2018 年 3 月，在巴黎举行的以太坊开发者大会上，Vitalik 发布了 Plasma Cash模型，可以视为对 Plasma MVP 的改进。Plasma Cash 与 Plasma MVP 的主要区别是每次存款操作都会产生一个唯一的 coin ID 对应转移到侧链上的资产，并使用一种称为稀疏梅克尔树（Sparse Merkle Tree）的数据结构存储交易历史。由此带来的好处是用户不需要关注子链上的每个动态，只需要关注跟自己的 token 有关的动态。
- plasma MVP
- plasma Cash
- Plasma  Debit
- Plasma Prime

#### MVP的问题
1. 在运营商行为不当的情况下，存在批量退出问题 
2. 挑战周期过长，而用户想要取钱，必须等待挑战期结束 
3. 用户需要监控 Plasma 链上的所有交易 

#### 参与者
`OMG Network`、`Loom Network`、`Matic Network`最早也是研发Plasma的.

但事后证明，Plasma 在资金退出等问题上非常复杂，导致其很难成为真正可行的可扩展性解决方案。但 Plasma 的失败并非没有用，`zk-Rollup` 的诞生让人们看到的 `layer-2` 扩展的可能方向，它可以解决 Plasma 的数据可用性问题和退出复杂性问题。`Plasma Group` 也将重心转向 `Optimistic Rollup`，这个团队我们后面介绍Rollup的时候也会说到。

从这里我们可以看出，很多的技术构想，包括 Vitalik 的构想从一开始也可能会有一定的局限性，只有在不断的摸索中才能找到真正可行的方向。社区的力量很重要，rollup 方案一开始就是由社区成员提出的，只有更多人参与进来，才能推动以太坊和公链生态的发展。伟大的力量总是根植于人们之中。


## 4.Rollup

第四部分介绍Rollup技术，这也是我们比较详细要聊的项目，也是在以太坊社区对扩容陷入困境时的救命稻草，幸运的是现在看来rollup技术会是众多layer2解决方案中最先落地的。

#### 工作模式
那什么是rollup，中文翻译过来是卷叠，rollup的核心理念是**将很多笔交易数据打包压缩，然后将处理后的数据同步到链上**。就是将计算和存储放到链下进行，结果最后上传到链上，很多文章的比喻是rollup是一层的草稿纸，其实很形象。**Rollup和上面介绍的Layer2扩容方案最大的区别就是数据可用性，Rollup会把数据提交到一层，也就继承了一层的安全性**。

那怎么保证上链数据的正确性呢？这其实可以分成两种方案`zk rollup`和`Optimistic rollup`。

#### zk rollup
zk rollup是通过有效性证明去保证上链数据的正确性，这里使用的是零知识证明。
zk rollup的特点:
- 最终性确认速度快
- 计算量大
- 暂时不兼容EVM

#### optimistic rollup
optimistic rollup则是延续了plasma的方法，通过欺诈证明去保证上线数据的正确。

optimistic rollup的特点：
- 有退出周期
- EVM兼容

#### 比较
对于zk和op其实还可以这样理解，Optimistic rollup，代表着乐观的看法，它是假设Layer2状态都正确，直到被证明不对，是一种被动的方式；zk是只有状态证明都正确，才会被写入链上，是一种主动的方式。乐观和悲观在技术视角里面还有很多其他应用，比如数据库里面的乐观锁和悲观锁。

op的特点之一是有退出周期，因为欺诈证明要保证链上数据的有效性，就会需要有挑战周期，挑战的意思就是用户之间就发布的数据产生了争议，质押保证金，提交争议，最后正确的一方获得对方的部分保证金，现在大部分协议的调整期都设置在7天左右，这也是op的缺陷，会降低用户资金的利用效率，不过现在也有一些解决方案，比如：流动性中间商提供了快速退出的方式，但是需要用户付一些手续费，还有markerDAO的dai桥也提供从二层快速提取dai的方式。
op的特点之二就是EVM的兼容性好，合约代码基本不需要怎么改，只需要注意timestamp和一些block对象中的属性表达的含义不同，[参考1](https://developer.offchainlabs.com/docs/solidity_support)

那zk rollup的特点包括确认速度快，从二层到一层能快速确认，也就是说用户没有等待周期，但是zk现阶段的缺点是计算量大，因为需要生成零知识证明需要大量计算，而且零知识证明电路的设计也很麻烦，所以目前是不兼容EVM的。


目前zk的头部开发团队是zksyc和startware。op的头部开发团队是Arbitrum和Optimism，Arbitrum和Optimism的协议大部分相同，不同的地方是挑战机制上不同，Optimism是重新执行完整的交易，Arbitrum采用的是多轮交互式证明，就是将规模大的争议细分成小的争议，直到找到最关键的那一步。这两者本质上是链上成本和解决争议时间的平衡。也就是说Optimism的解决争议的时间短，但是gas高，Arbitrum解决争议时间长，但是gas低，因为链上执行之需要执行关键的争议步骤，不需要重新完整执行。

> 在多轮互动的 Rollups 中，也有挑战时间窗口期，断言者和挑战者之间存在多轮交互，链上合约作为裁判，由它来决定谁是错误。多轮交互设计的主要考虑在于，尽可能减少解决争议的链上工作。通过在挑战者和断言者之间的多轮交互最大程度减少链上的工作量。

#### 参与方

zk Rollup
- Matter Labs(zkSync)
- StarkWare

Optimistic Rollup
- OffChain Labs(Arbitrum)
- Optimism

#### Arbitrum的工作模式
其中我这里主要介绍Arbitrum的工作模式

> Arbitrum 的系统主要由三部分组成（图中的右部分，从下到上）：EthBridge，AVM 执行环境和 ArbOS。
>
> EthBridge 主要实现了 inbox/outbox 管理以及 Rollup 协议。EthBridge 实现在 Layer1。
>
> ArbOS 在 AVM 虚拟机上执行 EVM。
>
> 简单的说，Arbitrum 在 Layer2 实现了 AVM 虚拟机，在虚拟机上再模拟 EVM 执行环境。用 AVM 再模拟 EVM 的原因是 AVM 的状态更好表达，便于 Layer1 进行挑战。

![arbitrum](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/2.png)

#### Rollup能多大程度上实现扩容？

在现有的以太坊链上，gas 上限是 1250 万，一笔交易里，每个字节的数据花费 16 gas。这意味着如果一个区块只包含一个 batch (我们说相当于打包了一个 zk Rollup，在证明验证上花费 50 万 gas)，那一个 batch 可以包含 (1200 万 / 16) 750,000 字节的数据。如上文所示，对于一个以太坊转账的 Rollup，每一次用户操作只需 12 个字节，也就是说该批交易可以包含高达 6.25 万笔交易。现在平均出块时间为 13 秒，即相当于大约每秒 4807 笔交易 (相比之下，现在直接在以太坊上转账是每秒 1250 万 /21000/13~=45 笔交易 ) 

![matterlabs](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20210722/5.png)