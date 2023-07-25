---
layout: post
title:  "Intro Layer3"
date:   2023-05-06
categories: intro
author: zx
location: ShangHai, China
cover: ""
description: ""
---
---
[Vitalik：什么样的 Layer3 才有意义？](https://vitalik.ca/general/2022/09/17/layer_3.html)
里面主要介绍了layer3的三个愿景：

- L2 用于扩容，L3 用于定制功能，例如隐私。在这个愿景中，没有尝试提供「可扩展性二次方增长」；相反，堆栈中有一层可以帮助应用程序扩展，然后有独立的层来满足不同用例的定制功能需求。

- L2 用于通用扩容，L3 用于可定制化扩容。可定制化扩容可能有不同的形式：使用除 EVM 之外的其他东西进行计算的专用应用程序，数据压缩针对特定应用程序的数据格式进行优化的 rollups（包括每个块中将「数据」与「证明」分开，并用单个 SNARK 替换证明）等。

- L2 用于无信任扩展（rollups），L3 用于弱信任扩展（validiums）。 Validium 是使用 SNARK 来验证计算的系统，但将数据可用性留给受信任的第三方或委员会。在我看来，Validium 被严重低估了：特别是，许多「企业区块链」应用程序实际上可能最好由运行 validium 的证明者提供服务，并定期将哈希提交到链的集中式服务器来提供最佳服务。Validium 的安全等级低于 rollups，但可以便宜得多。



[zkSync 将 Layer3 测试网络「Pathfinder」重新命名为「Opportunity」](https://twitter.com/zksync/status/1580647272073027585)

这里讨论的 Layer 2 以及 Layer 3 的 SDKs 主要是基于 ZK Rollups，而不是 Optimistic Rollups。因为 Optimistic Rollups 的欺诈验证，存在一个 7 天的存取等待期限，这提高了技术上的复杂性。


Lens基于Optimistic的L3扩展方案Momoka，Momoka 作为一种扩展解决方案，可以在链下处理 Polygon 交易，实现超大规模扩容并降低成本。与 L2 解决方案不同，Momoka 不会将交易压缩到 L1 中，而是将它们发送并存储在数据可用性层上。
https://www.theblockbeats.info/flash/136776?search=1

## Layer2 to Layer3
各个layer2都相继推出layer3的项目
- [arbitrum orbit](https://arbitrum.foundation/orbit)
- [zksync Opportunity](https://blog.matter-labs.io/zksync-l3-pathfinder-to-hit-testnet-in-q1-2023-367a425592db)
- [optimism stack](https://stack.optimism.io/)
- [StarkWare layer3](https://medium.com/starkware/fractal-scaling-from-l2-to-l3-7fe238ecfb4f)



## Layer3 

### related projects(including DA)
- bundlr network 
    链接：https://bundlr.network/
    基于Arweave去中戏化存储
- nautchain
    链接：https://nautchain.xyz/#/
    第一个流支付的layer3
- Momoka
    链接：https://momoka.lens.xyz/
    Web3 社交协议 Lens Protocol做的Optimistic L3 扩展解决方案，
    旨在将以超大规模扩展处理交易和支持下一代 Web3 社交用户。




