---
title: 区块链开发之ETH离线签名和其代币离线签名
date: 2018-08-02 13:35:02
tags:
 - 区块链
 - BTC
 - ETH
categories: 区块链
---
# 引言 #
eth的离线签名没有btc那么繁琐，只需要设置好nonce，gasprice和gaslimit即可

eth离线签名
```java
/**
     * 离线签名eth
     *
     * @param to//转账的钱包地址
     * @param nonce//获取到的交易次数
     * @param gasPrice
     * @param gasLimit
     * @param value           //转账的值
     * @return
     */
    public static String signedEthTransactionData(String privateKey, String to, BigInteger nonce, BigInteger gasPrice, BigInteger gasLimit, String value) throws Exception {
        if (words == null || words.size() != 12) {
            throw new RuntimeException("please generateMnemonic first");
        }
        //把十进制的转换成ETH的Wei, 1ETH = 10^18 Wei
        BigDecimal realValue = Convert.toWei(value, Convert.Unit.ETHER);
        RawTransaction rawTransaction = RawTransaction.createEtherTransaction(nonce, gasPrice, gasLimit, to, realValue.toBigIntegerExact());
        //手续费= (gasPrice * gasLimit ) / 10^18 ether

        Credentials credentials = Credentials.create(privateKey);
        //使用TransactionEncoder对RawTransaction进行签名操作
        byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
        //        //转换成0x开头的字符串
        return Numeric.toHexString(signedMessage);
    }
```

代币转账代码：
```java
    /**
     * 离线签名eth
     *
     * @param contractAddress//合约地址
     * @param to//转账的钱包地址
     * @param nonce//获取到的交易次数
     * @param gasPrice
     * @param gasLimit
     * @param value                 //转账的值
     * @return
     */
    public static String signedEthContractTransactionData(String privateKey, String contractAddress, String to, BigInteger nonce, BigInteger gasPrice, BigInteger gasLimit, Double value, Double decimal) throws Exception {
        if (words == null || words.size() != 12) {
            throw new RuntimeException("please generateMnemonic first");
        }
        //因为每个代币可以规定自己的小数位, 所以实际的转账值=数值 * 10^小数位
        BigDecimal realValue = BigDecimal.valueOf(value * Math.pow(10.0, decimal));

        //0xa9059cbb代表某个代币的转账方法hex(transfer) + 对方的转账地址hex + 转账的值的hex
        String data = "0xa9059cbb" + Numeric.toHexStringNoPrefixZeroPadded(Numeric.toBigInt(to), 64) + Numeric.toHexStringNoPrefixZeroPadded(realValue.toBigInteger(), 64);
        RawTransaction rawTransaction = RawTransaction.createTransaction(nonce, gasPrice, gasLimit, contractAddress, data);
        //手续费= (gasPrice * gasLimit ) / 10^18 ether

        Credentials credentials = Credentials.create(privateKey);
        //使用TransactionEncoder对RawTransaction进行签名操作
        byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
        //转换成0x开头的字符串
        return Numeric.toHexString(signedMessage);
    }
```