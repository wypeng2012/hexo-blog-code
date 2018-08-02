---
title: 区块链开发之获取BTC，ETH的余额，BTC的UTXO列表，ETH的nonce
date: 2018-08-02 10:55:55
tags:
 - 区块链
 - ETH
 - BTC
categories: 区块链
---
# 获取eth的余额 #

先来说说eth的余额获取，这个是很简单的，直接通过节点的rpc接口，就可以获取到，具体使用到的RPC接口为：**eth_getBalance**

## 参数： ##
1.要查询钱包地址
2.查询不同状态的余额"latest"（已经确认了的）, "earliest"（创世区块的） ， "pending"（包含未确认的交易的余额）

```java
params: [
   '0xc94770007dda54cF92009BFF0dE90c06F603a09f',
   'latest'
]
```

## 示例： ##
```base
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```

# 获取BTC的余额 和 UTXO列表 #
直接使用btc节点的rpc服务是很难查出某个地址的余额的，因为btc的utxo机制，想通过rpc服务查余额，就只能先把地址导入节点，节点扫描整个区块，维护本地utxo列表，然后才能查询余额，这需要耗费一定的时间（20分钟以上），所以这个方式根本不适合查询余额。最终只能自己搭建一个btc的区块链浏览器，来解决这个问题（同时也解决了获取utxo列表和查询历史记录的问题）。使用的比较广的btc浏览器应该是insight-api，它的代码是开源的，并且提供接口，github地址：[https://github.com/bitpay/insight-api](https://github.com/bitpay/insight-api)

他提供的接口，足以满足你的需求

# 获取eth的nonce #
估计有些人会有疑问，nonce是用来干啥的呢？其实他是eth用来防止双花的机制：

为了防止交易的重播攻击，每笔交易必须有一个nonce随机数，针对每一个账户nonce都是从0开始，当nonce为0的交易处理完之后，才会处理nonce为1的交易，并依次加1的交易才会被处理。以下是nonce使用的几条规则：

当nonce太小，交易会被直接拒绝。

当nonce太大，交易会一直处于队列之中；

当发送一个比较大的nonce值，然后补齐开始nonce到那个值之间的nonce，那么交易依旧可以被执行。

那如何获取这个nonce和防止交易失败呢，那就应该使用rpc接口获取，并且获取pending状态的值，具体rpc接口为：**eth_getTransactionCount**
## 参数： ##
1.要查询钱包地址
2.查询不同状态的余额"latest"（已经确认了的）, "earliest"（创世区块的） ， "pending"（包含未确认的交易的次数，推荐使用这个）

```java
params: [
   '0xc94770007dda54cF92009BFF0dE90c06F603a09f',
   'latest'
]
```

## 示例： ##
```base
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```