---
layout: post
title:  "the next step of wallet —— did"
date:   2022-11-20
categories: project
author: zx
location: ShangHai, China
cover: ""
description: ""
---
---

以下是关于钱包和did结合的一个设想，让我们从行业背景故事开始。
## 背景：什么是did？
传统世界，[did](https://www.w3.org/TR/did-core/)在w3c的标准里一直都处于草稿阶段，直到今年7月，[去中心化标识符（DIDs）v1.0 成为 W3C 正式推荐标准](https://www.w3.org/2022/07/pressrelease-did-rec.html.zh)，根据标准的定义，DIDs是一种新型标识符，支持可验证的、去中心化的数字身份。标准中列举了DIDs的应用如下：
> - 政府：美国、加拿大以及欧盟正在探索使用 DIDs 为其企业和居民提供保护隐私的数字身份文件，让这些主体能够选择如何以及何时共享他们的数据。
> - 零售商：美国的便利店、杂货店、餐馆、酒吧和消费品公司正在将 DIDs 用于新的数字年龄验证 程序，以提高隐私、结账速度，并在用户购买有年龄限制的产品时阻止使用虚假身份证件。
> - 供应链利益相关方：全球的政府监管机构、贸易标准体系、供应商、运货商和零售商正在使用,DIDs 探索新系统从而更准确地验证产品和服务的来源和目的地，这将简化并使得数据传送能够适用正确关税、防止倾销以及监测转运。
> - 从业者：大学、职业培训计划和教育标准组织正在采用 DIDs，以便在其申请高等教育或工作职位时，颁发由毕业生控制和共享的数字学习证书。

![did](https://www.w3.org/TR/did-core/diagrams/parts-of-a-did.svg)

对于文章中目前列举的应用场景来说，都是从目前的现实出发，通过DIDs去解决例如隐私、虚假身份的问题。
那么如果我们从互联网那么多年的发展历史以及目前区块链十多年的发展来看，那么did的最重要的点，一是**个人真正意义上拥有自己的身份**，二是**身份在不同场景的互通**。

在web2的世界里，你所认为的属于你的资产其实都属于平台和公司，他们有权处置你的账号，比如封号，比如杀熟，用户不过是大平台的商品，广告商才是他们真正的用户。早在19年，一篇[《百度已死》](https://www.huxiu.com/article/282406.html)的文章就在互联网流传，让互联网的围墙花园暴露在世人面前，各个平台和公司都是独立的，大家都在造轮子，浪费生产力，用户不过是各家大平台争夺的资产，从那时人们才开始真正意识到**我们从来没有从真正意义上拥有自己的身份**。每个平台为了增加用户粘性抢夺用户时间（留存）无所不用其极，每一个平台都尽可能多的去获取你更多的信息，请求你更多的权限，我们在各大平台间疲于奔命。这些账户/用户/数据也成为了各大平台的护城河，**我们的身份数据都是孤零零的商品，被各个平台分别保存**。

在v神的文章《去中心化社会：寻找 Web3 的灵魂》中说到，“今天的 Web3 多用来表述可转让的金融化资产，⽽非用于对社会信任关系编码。⽽诸多核心经济活动，如无抵押贷款和建设个人品牌，都是建立在长期而不可转让的关系之上的。”

在web3里，从最底层的去中心化保证了用户拥有自己的身份以及身份的互通。那么到目前为止，DID在区块链里面的位置一直都不冷不热，有人研究，相对来说不是那么冷门，每过一段时间就会有新文章介绍did，比如前一段时间v神在一篇文章中介绍的灵魂绑定代币（soul bond token）又让这个did重新进入人们的视野，但是热度也不如DeFi、跨链、扩容等一些区块链方向。

那么在区块链里面又有哪些项目在探索这个方向呢。

## 当前市场分析
目前市场上有很多项目都在做一些关于did的探索，只不过切入点不同而已。
从产品方向可以简单划分为三类，
1. 社交网络/图谱 Social Network
2. 身份 Social Profile & DID
3. 链上声誉 Reputation

![did](https://image.theblockbeats.info/upload/2022-03-01/3625ea69c7a82537d085650f5252147839ff8c44.png?x-oss-process=image/quality,q_90/watermark,image_d2F0ZXJtYXJrbmV3LnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSxQXzIw,g_center)

### 社交网络/图谱 Social Network
1. [Lens Protocol](https://www.lens.xyz/)
该项目由 AAVE 团队开发，目标是让用户创建自己的信息，可供各种 dapp 读取，也就是打造一个用于所有 web3 社交的协议（基础设施）。

2. [ShowMe](https://showme.fan/)
ShowMe 有点像豆瓣小组，个人和项目方都可以创建自己的 club，也可以加入其他 club，都需要 claim 一个 nft，各 club 可以通过发布一系列的任务激励社员，让彼此联系更为紧密。

3. [Mem protocol](https://mem.co/)
a16z 领投，目前只有一个网页和推特，网站只有一行字"Sign in as you"。根据结束Mem 正在为 web3 社交构建以人为本的工具，让你拥有自己的朋友图谱，浏览区块链并通过分享知识赚取收益。Mem 已经建立了一个个人资料浏览器，并开始让新成员加入他们可验证的链上身份。同时，Mem 构建了一个界面来浏览以太坊区块链上钱包的链上数据，并开始设计一个开放协议来记录声称钱包和代币生成的交互和关系，计划于 2022 年初推出。

![social](https://image.theblockbeats.info/upload/2022-06-17/89038c91bd6664a0d1306af81d8aef6bee2af086.png?x-oss-process=image/quality,q_50/format,webp)

### 身份 Social Profile & DID
1. [ENS](https://ens.domains/)
以太坊名称服务（Ethereum Name Service，简称ENS）是一个基于以太坊区块链的分布式、开放和可扩展的命名系统。 ENS 的工作是将可读的名称（比如“alice.eth”）解析为计算机可以识别的标识符，如以太坊地址、其他加密货币地址、内容的哈希、元数据等。

2. [CyberConnect](https://cyberconnect.me/)
算是 web3 中的「老牌」社交项目，目标是通过钱包地址把用户连接起来并为 Dapp 提供用户数据。

3. [debank](https://debank.com/)
老牌资产管理工具，目前增加了一些社交元素

4. [unipass](https://unipass.id/)
UniPass 是为 Web3 用户搭建去中心化数字身份 (DID，Decentralized Identity) 的协议级应用，它的作用与电子邮件在互联网中的角色类似，旨在为传统互联网用户进入 Web3 铺平通路并降低使用门槛，助推 Web3 的大规模普及。

5. [DAS](https://www.did.id/)
跨链的身份系统，有点类似于一个跨链的ens

### 链上声誉 Reputation

1. [galxe](https://galxe.com/)
Project Galaxy是一个Web3凭证数据网络。 Project Galaxy建立在开放和协作的基础架构之上，可帮助Web3开发人员和项目利用数字凭证数据和NFT来构建更好的产品和社区。

2. [POAP](https://poap.xyz/)
POAP 的全称是Proof of Attendance Protocol (出席证明协议)，用户可以通过POAP领取参与过项目的一些徽章来证明自己参与过项目

3. [Layer3](https://beta.layer3.xyz/)
与其他主打社交的各个项目不同，layer3 致力于为人们提供 web3 上的就业机会

4. [gitcoin](https://gitcoin.co/)
Gitcoin 是一个开源软件开发激励平台，旨在通过区块链支持各类开源项目，促进开源运动的发展。在 Gitcoin 上，一个项目可以通过创建赏金或任务供其他人完成，而用户则可以赚取 Token 来完成上述任务。每个用户对任务的贡献将在链上可被追踪，并帮助其建立因完成具有明确目标任务的声誉。

上面所有的应用来说，都是根据用户数据建立模型，有的是通过已有的数据去分析建模，有的需要用户交互，通过领取徽章，关注，发送去建立模型。大家的标准并不统一。那么在这里我们需要做什么，答案是统一标准，怎么统一，提高开发者的集成和用户的使用，那么怎么提高，答案是钱包。

## 可以做什么

就下面流传的很广的一张图来说，web3本质实现身份和平台的解绑，痛点是管理各个平台不同的登录方式，身份是基于平台的，我们没办法迁移，一个事实是我们的身份不过是facebook的商品。（用户数据是强大的广告算法的关键要素，就是这些算法将 web2.0 社交网络货币化，产生了数百亿美元的收入，这些收入都流向了平台而不是用户本身。这种情况是...... 不可持续的）

![web1-2-3](https://image.theblockbeats.info/upload/2022-06-17/8d779a427f3a8b05c62ff130c5d36c8920841b2e.png?x-oss-process=image/quality,q_50/format,webp)


钱包是web3的入口，也是web3用户使用最多的场景，就metamask来说就超过了1000w用户。现在钱包大部分都只是资产管理的工具，用户资产和身份是一体的，并没有剥离，我们需要扩展钱包这个概念，我们做的是一个集成用户身份的插件，并且在我们的插件中，把这些数据进行标准化，能最后输出给开发者/项目方。

钱包的可组合性在[这篇文章](https://www.panewslab.com/zh/articledetails/D03958471.html)中有简单介绍。目前市场上，硬件钱包，插件钱包已经有了很好的集成，硬件钱包相当于安全层，插件钱包相当于交易和连接层，那在插件钱包之上，我们需要把用户身份抽离出来。其实就像支付宝和微信一样，支付宝想在app里面加入社交，但是一直不成功，其实就是因为，对于用户来说，转账是需要强安全性的操作，但是关注和聊天不是强安全性的，所以用户不愿意在支付宝里面去聊天。这点在metamask里面也是一样的，而且加密资产更加需要更高的安全性，所以对于用户来说，希望是资产和身份时隔离的。

![layer of wallet](https://image.panewslab.com/upload/pachong/20210521/21052111520635369865.jpeg)

我们通过区块链保证了个人真正意义上拥有自己的身份，那么身份互通怎么解决呢，比如不同的链之前的身份怎么互通呢，不同的游戏之前数据怎么能够打通呢。这个是我们的终极目标，这也是元宇宙的终极目标。前期会通过集成不同的链去适配不同的身份，用户可以通过这个插件去登录到不同的应用，实现应用之间的互通。

## 未来想象
对于区块链来说，这些年证明了万链统一到以太坊是走不通的，诞生类似polygon、Avalanche、bsc等等的一些新的layer1的链，对于以太坊自身来说，也想通过layer2实现以太坊的扩容。所以未来来说，以太坊可能只是结算层，各种其他layer1链或者layer2都是应用链。

所以对上述来说，一开始可能是基于某一条链（eg. ETH），未来会有一条代表着用户身份层的应用链，未来各种第三方登录都能够申请成为链的节点，链的身份层也能够集成web2的身份。


# 参考
- [Why Sign-In with Ethereum is a Game-Changer - Part 1](https://blog.spruceid.com/sign-in-with-ethereum-is-a-game-changer-part-1/)
- [Why Sign-In with Ethereum is a Game-Changer Part 2](https://blog.spruceid.com/why-sign-in-with-ethereum-is-a-game-changer-part-2/)
- [Vitalik-去中心化社会：寻找 Web3 的灵魂](https://web3caff.com/zh/archives/10440)
- [可组合钱包的重要性](https://www.panewslab.com/zh/articledetails/D03958471.html)
- [Web 3.0核心组件：身份管理会是下一个千亿市场吗？](https://www.theblockbeats.info/news/29425?search=1)
- [为什么你需要关注链上身份（DID）赛道？](https://www.theblockbeats.info/news/29538?search=1)
- [社会地位即服务, Status as a Service (一): 社交网络是一种 ICO 行为？](https://zhuanlan.zhihu.com/p/58307165)
- [在DeSoc语境下针对DID的价值发现与精神分析](https://www.theblockbeats.info/news/31590?search=1)
- [一文速览首次Gitcoin Web3社交生态轮](https://www.panewslab.com/zh/articledetails/64bs55b3.html)
- [谈Web3重塑数据价值](https://www.panewslab.com/zh/articledetails/1640692713457476.html)
- [万向区块链蜂巢研习社-构建更好的Web3：DID、数据主权、隐私](https://mp.weixin.qq.com/s/obErYjJ8AKZjRiGV-5A8UQ)
- [从Unipass和Keystone展开聊聊Web3 Login的安全、交互体验与发展时机](https://www.xiaoyuzhoufm.com/episode/62f752884723ce2f9c75eae8)