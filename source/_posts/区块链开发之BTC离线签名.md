---
title: 区块链开发之BTC离线签名
date: 2018-08-02 11:27:19
tags:
 - 区块链
 - BTC
categories: 区块链
---
# 引言 #
btc的账户系统是基于utxo模式的（utxo具体解释，大家可以自行搜索），如何获取utxo列表，可以看我前篇文章：[区块链开发之获取BTC，ETH的余额，BTC的UTXO列表，ETH的nonce](/2018/08/02/区块链开发之获取BTC，ETH的余额，BTC的UTXO列表，ETH的nonce/)

获取到的数据格式大致如下
```java
[
  {
    "address":"mo9ncXisMeAoXwqcV5EWuyncbmCcQN4rVs",
    "txid":"d5f8a96faccf79d4c087fa217627bb1120e83f8ea1a7d84b1de4277ead9bbac1",
    "vout":0,
    "scriptPubKey":"76a91453c0307d6851aa0ce7825ba883c6bd9ad242b48688ac",
    "amount":0.000006,
    "satoshis":600,
    "confirmations":0,
    "ts":1461349425
  },
  {
    "address": "mo9ncXisMeAoXwqcV5EWuyncbmCcQN4rVs",
    "txid": "bc9df3b92120feaee4edc80963d8ed59d6a78ea0defef3ec3cb374f2015bfc6e",
    "vout": 1,
    "scriptPubKey": "76a91453c0307d6851aa0ce7825ba883c6bd9ad242b48688ac",
    "amount": 0.12345678,
    "satoshis: 12345678,
    "confirmations": 1,
    "height": 300001
  }
]
```

具体代码如下
```java
   public static String signBTCTransactionData(@NonNull List<UnSpentBTC> unSpentBTCList, @NonNull String from, @NonNull String to, @NonNull String privateKey, long value, long fee) throws Exception {
        NetworkParameters networkParameters = null;
        if (!BTC_TEST_NET)
            networkParameters = MainNetParams.get();
        else
            networkParameters = TestNet3Params.get();

        Transaction transaction = new Transaction(networkParameters);
        DumpedPrivateKey dumpedPrivateKey = DumpedPrivateKey.fromBase58(networkParameters, privateKey);

        ECKey ecKey = dumpedPrivateKey.getKey();

        long totalMoney = 0;
        List<UTXO> utxos = new ArrayList<>();
        //遍历未花费列表，组装合适的item
        for (UnSpentBTC us : unSpentBTCList) {
            if (totalMoney >= (value + fee))
                break;
            UTXO utxo = new UTXO(Sha256Hash.wrap(us.getTxid()), us.getVout(), Coin.valueOf(us.getSatoshis()),
                    us.getHeight(), false, new Script(Hex.decode(us.getScriptPubKey())));
            utxos.add(utxo);
            totalMoney += us.getSatoshis();
        }

        transaction.addOutput(Coin.valueOf(value), Address.fromBase58(networkParameters, to));
        // transaction.

        //消费列表总金额 - 已经转账的金额 - 手续费 就等于需要返回给自己的金额了
        long balance = totalMoney - value - fee;
        //输出-转给自己
        if (balance > 0) {
            transaction.addOutput(Coin.valueOf(balance), Address.fromBase58(networkParameters, from));
        }
        //输入未消费列表项
        for (UTXO utxo : utxos) {
            TransactionOutPoint outPoint = new TransactionOutPoint(networkParameters, utxo.getIndex(), utxo.getHash());
            transaction.addSignedInput(outPoint, utxo.getScript(), ecKey, Transaction.SigHash.ALL, true);
        }

        return Hex.toHexString(transaction.bitcoinSerialize());
    }
```