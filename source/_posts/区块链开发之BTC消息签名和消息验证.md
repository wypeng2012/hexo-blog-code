---
title: 区块链开发之BTC消息签名和消息验证
date: 2018-08-03 13:34:45
tags:
 - 区块链
 - BTC
categories: 区块链
---
BTC的消息签名机制，是使用私钥对一段信息签名，然后使用公钥对此签名做校验，次机制可以作为验证消息真伪的手段，这里使用bitcoinj库实现的
> implementation ‘org.bitcoinj:bitcoinj-core:0.14.7’


# 对消息进行签名 #
```java
 /**
     * @param msg 要签名的信息
     * @param privateKey 私钥
     * @return
     */
    public static String signMsg(@NonNull String msg, @NonNull String privateKey) {
        NetworkParameters networkParameters = null;
        if (!BTC_TEST_NET)
            networkParameters = MainNetParams.get();
        else
            networkParameters = TestNet3Params.get();
        DumpedPrivateKey priKey = DumpedPrivateKey.fromBase58(networkParameters, privateKey);
        ECKey ecKey = priKey.getKey();
        return ecKey.signMessage(msg);
    }
```
# 验证签名消息 #
```java
/**
     * @param msg 明文
     * @param signatureMsg 签名好的信息
     * @param pubkey 公钥
     * @return
     */
    public static boolean verifyMessage(@NonNull String msg, @NonNull String signatureMsg, @NonNull String pubkey) {
        boolean result = false;
        ECKey ecKey = ECKey.fromPublicOnly(Utils.HEX.decode(pubkey));
        try {
            ecKey.verifyMessage(msg, signatureMsg);
            result = true;
        } catch (SignatureException e) {
            result = false;
            e.printStackTrace();
        } finally {
            return result;
        }

    }

```