---
title: 区块链开发之确定性算法bip32，bip39，bip44
date: 2018-07-30 17:34:40
tags: 区块链
categories: 区块链
---
# 引言 #
随着比特币区块链的发展，人们已经不满足于，只有一个账号的情况，有些人会有好几个账户，但是这就出现一个问题，我有几个账号，就要保存几个私钥，这就特别麻烦和不友好，所以，就出现了bip32确定性算法，该算法可以让你只有同一个种子，就可以生成无数个私钥和地址，这就大大方便了用户的使用。但是这个种子，也不较长，用户使用起来也比较繁琐，这就出现了bip39，它是使用助记词的方式，生成种子的，这样用户只需要记住，12个单词（3,6,9,12，15,18,21，24支持这些单词数，目前使用较广泛的是12和24），这就有大大提高了用户使用的便利性。又随着区块链发展，市面上出现了很多币种，之前的确定性算法只是针对比特币的，也就是说只支持一种币种，用户想用同一个种子，管理不同币种，这就促使了bip39协议的出现，它是基于bip32协议的，它给bip32的路径，赋予了不同的意义，很好的解决了多币种，多地址的问题。

# 协议官方链接 #
如果想详细的了解这些协议，请查看官方文档（解析的最清楚）
big32：[https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)

bip39：[https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)

bip44：[https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki)

验证网站：[https://iancoleman.io/bip39/](https://iancoleman.io/bip39/)

# BIP44简介 #

## 路径级别 ##
bip44给bip32定义了5各级别
> m / purpose' / coin_type' / account' / change / address_index

**m是固定的**


**Purpose也是固定的，值为44（或者 0x8000002C）**

## Coin type ##
这个代表的是币种，0代表比特币，1代表比特币测试链，60代表以太坊

完整的币种列表地址：[https://github.com/satoshilabs/slips/blob/master/slip-0044.md](https://github.com/satoshilabs/slips/blob/master/slip-0044.md)

## Account ##
代表这个币的账户索引，从0开始

## Change ##
常量0用于外部链，常量1用于内部链（也称为更改地址）。外部链用于在钱包外可见的地址（例如，用于接收付款）。内部链用于在钱包外部不可见的地址，用于返回交易变更。 **(所以一般使用0)**

## address_index ##

这就是地址索引，从0开始，代表生成第几个地址，**并且官方建议，每个account下的address_index不要超过20**

## 示例 ##

| coin | account | change | address | path |
| ------ | ------ | ------ | ------ | ------ |
| Bitcoin | first | external | first | m / 44' / 0' / 0' / 0 / 0 |
| Bitcoin | first | external | second | m / 44' / 0' / 0' / 0 / 1 |
| Bitcoin | first | internal | second | m / 44' / 0' / 0' / 1 / 1 |
| Bitcoin Testnet | first | external | first | m / 44' / 1' / 0' / 0 / 0 |
| Bitcoin Testnet | second | external | first | m / 44' / 1' / 1' / 0 / 0 |