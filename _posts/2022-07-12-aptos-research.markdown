---
layout: post
title:  "Aptos Research"
date:   2022-07-12
categories: 分享 
author: zx
location: ShangHai, China
cover: "https://zxblog.oss-cn-hangzhou.aliyuncs.com/20220812/1.png"
description: "给团队介绍最近热度非常高的一个项目aptos"
---
---

大家下午好👋，今天我给大家介绍最近热度非常高的一个项目aptos，本次分享的目的不是分享财富密码啥的，主要一个是我们钱包可能要接入一些新公链看能不能蹭蹭热度，看看我们tele钱包接入aptos的成本，另外一个是看一下他们有什么可以借鉴的地方。

下面👇是aptos的一些链接。

- 官网：[https://aptoslabs.com/](https://aptoslabs.com/)
- Devnet explorer: [https://explorer.devnet.aptos.dev/](https://explorer.devnet.aptos.dev/)
- 开发者文档：[https://aptos.dev/](https://aptos.dev/)
- github：[https://github.com/aptos-labs](https://github.com/aptos-labs)


我们接下来进入主题。

## 一、背景
> 2021 年创立，主要领导者 Mo Shaikh 与 Avery Ching 也曾是 Diem（原 Libra）与 Novi（原 Calibra）的主要构建者。

Aptos最近能够热度这么高，首先跟他们的背景分不开，那么首先我们来看一下aptos的背景，aptos是在2021年创立，它的两个主要领导者都来自meta下面的两个项目，稳定币项目`Diem`和数字钱包项目`Novi`，但是因为合规和政策问题，这两个项目都相继关闭了。所以他们从meta离职出来，创立了Aptos，并且使用了Diem项目的语言move，这个后面会有简要介绍，也算是继承了衣钵。

其实除了aptos之外，还有另外两个公链项目，也都是meta背景，也都是使用的MOVE语言，也都是a16z投资的，一个是`sui`、另外一个是`linera`

他们热度这么高的另一个原因是资本的推动，他们投资方非常豪华，公链叙事还没有结束，我觉得一部分是公链在不可能三角下还有很多可以完善的地方（比如安全性上面solana最近就有一些安全问题、可扩展性里面eth就很差、去中心程度也不高，solana之前宕机了很多次，然后最近数千个Solana钱包遭黑客攻击），另外一部分`solana`、`avax`让资本尝到了甜头，他们后续肯定会有新的动作。

## 二、Aptos简介

那么Aptos这个区块链有什么特点呢，从Aptos主网有这么一句话**Building the safest and most scalable Layer 1 blockchain.**主要强调了安全性和可扩展性。关于安全性这块他们之前在文章中有提到，一个是Move语言，安全灵活的区块链语言，另外一个是生产级、高保证 BFT 共识协议，还有就是，更安全的帐户密钥恢复和轮换协议

> 为了防止密钥被盗，Aptos 支持任何帐户轮换其私钥的能力。验证者还可以定期轮换他们的共识密钥，以提高安全性。为了避免由于密钥丢失而导致数十亿美元价值被锁定的问题，我们还致力于开发可直接集成到区块链账户模型中的密钥恢复新技术。

ref: [https://aptos.dev/guides/getting-started](https://aptos.dev/guides/getting-started)

全新Layer1公链项目，专注于安全性、可扩展性和可升级性。

目前处于开发者网络测试阶段，每周四重置，9月份上线主网，Roadmap参考：[https://medium.com/aptoslabs/the-aptos-vision-1028ac56676e](https://medium.com/aptoslabs/the-aptos-vision-1028ac56676e)
当前该网络每秒可处理1万笔以上的交易，理想状态下，Aptos 主网每秒可处理16万笔交易。

### 1. 账户
Aptos 区块链支持两种签名方案：Ed25519 和 MultiEd25519（用于多签交易）。
每个账户还存储一个 sequence_number，代表下一个交易的序列号，防止交易重放攻击。 在创建帐户时将其初始化为 0。
具体可以参考：[https://aptos.dev/concepts/basics-accounts](https://aptos.dev/concepts/basics-accounts)

### 2. Gas and Transaction Fees
`max_gas = gas_price * max_gas_amount`
  - max_gas_amount：最大gas消耗数量
  - gas_price：gas价格，类似以太坊的gas price，也是竞价机制

### 3. Full Nodes

FullNodes 可以由任何人运行。FullNode 重新执行 Aptos 区块链历史上的所有交易。FullNodes 通过与上游参与者（例如，其他 FullNodes 或验证者节点）同步来复制区块链的整个状态。FullNode 不参与共识。

第三方区块链浏览器、钱包、交易所和 DApp 可以运行本地 FullNode 以：

- 利用 REST 接口进行区块链交互。
- 获得 Aptos 账本的一致视图。
- 避免读取流量的速率限制。
- 对历史数据运行自定义分析。
- 获取有关特定链上事件的通知。

### 4. Validator Nodes
当交易提交到 Aptos 时，Validator节点运行分布式共识协议，执行交易，并将交易和执行结果存储在链上。Validator决定哪些交易将被添加到区块链中以及添加顺序。

![Validator Nodes](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20220812/2.png)

## 三、生态

### 钱包

#### 1. Martian Wallet
[http://martianwallet.xyz/](http://martianwallet.xyz/)
Martian Wallet 是 Aptos 链上的钱包，可用于管理数字资产，以及访问 Aptos 区块链上的去中心化应用程序。当前已推出 Chrome 上的插件钱包，之后将推出 ios 版本。除此之外，该项目还支持开发者铸造 NFT。

#### 2. Fewcha Wallet
[https://fewcha.app/](https://fewcha.app/)
Fewcha 是 Aptos 上的 Web3.0 钱包，据其描述由一个拥有 15 年以上软件开发经验和 5 年区块链工作经验的团队开发。Fewcha 还支持 Chrome、Brave、Edge、Firefox 多个浏览器。

#### 3. pontem wallet
- Github: [https://github.com/pontem-network/pontem-wallet/releases](https://github.com/pontem-network/pontem-wallet/releases)
- Chrome: [https://chrome.google.com/webstore/detail/pontem-wallet/phkbamefinggmakgklpkljjmgibohnba](https://chrome.google.com/webstore/detail/pontem-wallet/phkbamefinggmakgklpkljjmgibohnba)
仿照metamask修改的，由Pontem开发，Pontem 是一家产品开发工作室，与 Aptos 合作构建基础 dApp 和其他基础设施，除了钱包之外，他们还有构建在aptos上的dex [https://liquidswap.com/#/](https://liquidswap.com/#/)

![ecosystem](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20220812/3.png)

## 四、Aptos TS/JS SDK

参考：[https://aptos-labs.github.io/ts-sdk-doc/#aptos-tsjs-sdk](https://aptos-labs.github.io/ts-sdk-doc/#aptos-tsjs-sdk)
- AptosAccount：创建和管理aptos账户
```javascript
/**
* privateKeyBytes - 生成帐户密钥对的私钥
* address - 帐户地址
**/
new AptosAccount(privateKeyBytes?: Uint8Array | undefined, address?: MaybeHexString)
```

- AptosClient：提供从 Aptos 节点检索数据的方法。 有关更详细的 API 规范，参考[https://fullnode.devnet.aptoslabs.com/spec.html#/](https://fullnode.devnet.aptoslabs.com/spec.html#/)
```javascript
/**
* nodeUrl - Aptos Node API 端点的 url
* config - 配置参考axios
**/
new AptosClient(nodeUrl: string, config?: AptosClientConfig): AptosClient
```

## 五、MOVE语言

Move语言主要是由Diem团队开发，Aptos、Sui和Linera等一系列“meta系”公链使用move都是基于此进行了一些修改。

move白皮书：[https://developers.diem.com/docs/technical-papers/move-paper/](https://developers.diem.com/docs/technical-papers/move-paper/)

Move继承了rust语言的特性。最主要的特点是面向资源编程。

Move 的主要特点是能够定义自定义资源类型，从统计上确保资源只能在程序存储位置之间移动，而不能克隆或删除。此实现通过有效防止上述漏洞等问题显着提高了安全性。然后资源由移动模块管理，类似于智能合约，因为它们控制对创建、更新和删除已声明资源的规则进行编码的过程。Move 模块相对于智能合约的好处是它们强制执行数据抽象，这意味着资源在其声明模块内部是透明的，在其外部是不透明的。此外，在表达性规范语言的帮助下，Move 证明者可以正式验证 Move 模块的特征，并且足够高效，可用于持续集成测试。

Token: [https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/sources/token.move](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/sources/token.move)

Coin: [https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-framework/sources/coin.move](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-framework/sources/coin.move)

![compare](https://zxblog.oss-cn-hangzhou.aliyuncs.com/20220812/4.png)

Move 语言虽然极具创新性，但是新语言同时也会增加一定的学习成本，此外，在生态安全、审计、开发基础设施等方面都需要一定的重新学习和搭建时间，Aptos 如何吸引更多的开发者来共建生态的未来，确保上层应用的足够安全仍是不可避免的问题。

## 参考

[1][https://move-language.github.io/move/](https://move-language.github.io/move/)

[2][https://aptos.dev/guides/move-guides/move-on-aptos](https://aptos.dev/guides/move-guides/move-on-aptos)