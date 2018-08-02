---
title: 获取ETH指定代币的余额
date: 2018-08-02 15:10:06
tags:
 - 区块链
 - ETH
categories: 区块链
---
# 引言 #
之前的文章介绍了获取eth余额的方法和离线签名的方法（指定代币的离线签名），今天就来介绍一下如何获取满足ERC20协议的代币余额的。

要想获取代币的余额，要通过rpc接口得到接口为：**eth_call**

**参数**

1.object字段：

- from: 钱包地址
- to: 代币地址（智能合约地址）
- data：0x70a08231000000000000000000000000b60e8dd61c5d32be8058bb8eb970870f07233155

> **data数据格式：最前边的“0x70a08231000000000000000000000000”是固定的，后边的是钱包地址（不带“0x”前缀）**

2.QUANTITY|TAG，"latest", "earliest" or "pending"

参数示例
```base
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "data": "0x70a08231000000000000000000000000b60e8dd61c5d32be8058bb8eb970870f072331555675"
},"latest"]
```

请求示例
```base
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_call","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x"
}
```
> 注：result字段的值，就是余额