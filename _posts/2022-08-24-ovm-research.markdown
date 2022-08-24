---
layout: post
title:  "compatibility between Optimism & Ethereum Layer1"
date:   2022-08-24
categories: 分享 
author: zx
location: ShangHai, China
cover: ""
description: "在Optimism上开发合约的一些不一样的点"
---
---
## 网络情况
- 主网浏览器：[https://optimistic.etherscan.io/txs](https://optimistic.etherscan.io/txs)
- 测试网kovan浏览器：[https://kovan-optimistic.etherscan.io/](https://kovan-optimistic.etherscan.io/)
- 测试网goerli浏览器：[https://blockscout.com/optimism/goerli/](https://blockscout.com/optimism/goerli/)

Optimism Kovan将要被弃用，取而代之的是 Optimism Goerli，目前Alchemy、Infura都已经支持了 Optimism Goerli

10 月 5 日，Optimism Kovan 将全面退役<div id="refer-anchor-1"></div>


## EVM等效性

Optimism 客户端软件，是一个几乎完全是原版的Geth. 这意味着 Optimism 在本质上与以太坊几乎相同。特别是Optimism 共享同一个以太坊虚拟机，相同的账户和状态结构，以及相同的燃气计量机制和收费表. 我们将此架构称为[“EVM 等效”](https://medium.com/ethereum-optimism/introducing-evm-equivalence-5c2021deb306)这意味着大多数以太坊工具（即使是最复杂的工具）“只适用于”Optimism。 

> In a multi-chain world, “write once, deploy everywhere” becomes critical. 

## 修改的操作码
- `block.difficulty`：设置为0
- `block.basefee`：暂时不支持
- `tx.origin`：如果是 L1 ⇒ L2 的交易，则 tx.origin 设置为设置为 L1 ⇒ L2 交易的地址的**别名地址**。否则，此操作码正常运行。
- `block.coinbase`：由sequencer设置，当前设置为固定地址 OvmFeeWallet (0x4200...0011)
- `block.number`：对应于当前L2区块号，但是，从 Optimism 的 OVM 2.0 版本（2021 年 11 月）开始，**L2 上的每笔交易都放置在一个单独的块中，并且块不是以恒定速率产生的。**
- `L1BLOCKNUMBER`：返回 L2 已知的最后一个 L1 的区块号。通常，这个区块号将比实际的最新 L1 块号滞后 15 分钟<div id="refer-anchor-2"></div>

```javascript
import { iOVM_L1BlockNumber } from "@eth-optimism/contracts/L2/predeploys/iOVM_L1BlockNumber.sol";
import { Lib_PredeployAddresses } from "@eth-optimism/contracts/libraries/constants/Lib_PredeployAddresses.sol";

contract MyContract {
  function myFunction() public {
     uint256 l1BlockNumber = iOVM_L1BlockNumber(
         Lib_PredeployAddresses.L1_BLOCK_NUMBER // located at 0x4200000000000000000000000000000000000013
     ).getL1BlockNumber();
  }
}
```

- `block.timestamp`：使用交易本身的时间戳。每十五秒更新一次。Sequencer 维护了 `LatestL1Timestamp`，每 `timestampRefreshThreshold` 更新一次 `LatestL1Timestamp`。目前`timestampRefreshThreshold` 配置为 15 秒 (参考 [ROLLUP_TIMESTAMP_REFRESH](https://github.com/ethereum-optimism/optimism/blob/e631c39cb4abca63bbbce52052df1c5fc85f5c75/l2geth/scripts/start.sh#L13) 和 [浏览器](https://optimistic.etherscan.io/txs?p=10000))。由于这个原因，Uniswap在Optimism上就不太适合提供价格预言机的功能，[Uniswap官方](https://docs.uniswap.org/protocol/concepts/V3-overview/oracle#oracles-integrations-on-layer-2-rollups)也提醒避免在Optimism上使用oracle的功能

## 为什么需要 Address Alias
对于 L1->L2 消息，如果 msg.sender 为合约，那么在 L2 执行时，Origin 会被加上偏移。这是出于安全考虑，举例如下：

首先，攻击者在 L2 构造一个开放源码的合约，如 Uniswap pair，并诱导用户对合约授权 (approve())

然后，攻击者在 L1 同一地址部署恶意合约，并通过该恶意合约向 L2 传递攻击消息，取出用户授权的 ERC20 token；参数如下：

- msg.sender
  - Uniswap pair (L2) / 恶意合约 (L1)
- _target
  - ERC20 地址
- _data
  - transferFrom(userAddress, attackerAddress, amount)

#### 解决方案：

经过讨论：[What should the value of tx.origin and msg.sender be for L1 to L2 transactions? #1480](https://github.com/ethereum-optimism/optimism/discussions/1480)，决定参考 Arbitrum 的方案，通过 Address Alias 对msg.sender 地址做了偏移；由于哈希的抗碰撞性，攻击者无法计算相关参数 (如 Create nonce 或 Create2 salt)，因此无法将合约部署在偏移前的地址。

案例：[2000万OP被盗背后](https://www.theblockbeats.info/news/30817?search=1)

## Gas Fee

#### L2交易

在L2上发生的交易由两部分费用，一部分是L2交易执行费，另外一部分是L1数据费，也就是数据同步到L1层上链的费用

- L2 execution fee
`l2_execution_fee = transaction_gas_price * l2_gas_used`
Gas 价格会随着时间和拥堵而波动，可以通过[https://public-grafana.optimism.io/d/9hkhMxn7z/public-dashboard?orgId=1&refresh=5m](https://public-grafana.optimism.io/d/9hkhMxn7z/public-dashboard?orgId=1&refresh=5m)查看当前gas_price，目前，eth_gasPrice API 返回的 gasPrice 都是 0.001 Gwei

- L1 data/security fee
`l1_data_fee = l1_gas_price * (tx_data_gas + fixed_overhead) * dynamic_overhead`
`l1_data_fee`受四个因素影响，第一个是当前以太坊`gas_price`，`tx_data_gas `可以通过下面的公式计算`tx_data_gas = count_zero_bytes(tx_data) * 4 + count_non_zero_bytes(tx_data) * 16` ，`fixed_overhead` 是一个固定gas，目前为2100，`dynamic_overhead` 是放大系数，目前为**1.24**<div id="refer-anchor-3"></div>

#### 估算 L2 执行费用

可以通过将 `gas_price * gas_limit` 来估算 L2 执行费用，就像在以太坊上一样。

####  估算 L1 数据费用
可以使用 SDK `GasPriceOracle`或者可以使用位于以下位置的预部署智能合约估算 L1 数据费用`0x420000000000000000000000000000000000000F` . 合约`_GasPriceOracle` 位于每个 Optimism 网络（主网和测试网）上的相同地址。为此，请调用`GasPriceOracle.getL1Fee`(`<unsigned RLP encoded transaction>`)。

## 参考
- [1][kovan to goerli](https://dev.optimism.io/kovan-to-goerli/)
- [2][added opcodes](https://community.optimism.io/docs/developers/build/differences/#added-opcodes)
- [3][twitter](https://twitter.com/optimismFND/status/1479840872954875905?lang=en)