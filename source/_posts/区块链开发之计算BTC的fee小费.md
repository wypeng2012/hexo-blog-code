---
title: 区块链开发之计算BTC的fee小费
date: 2018-08-02 13:43:51
tags:
 - 区块链
 - BTC
categories: 区块链
---
# 引言 #
btc的fee也是要经过计算得出的，而不是随便给的，它的计价方式是按照每笔交易的字节数收的，所以要先计算出你这比交易的fee，必须先计算出这笔交易可能的字节数，计算公式如下：
> 148 x inputNum + 34 x outputNum + 10

算出字节数后，再乘以rate（Satoshi/byte）,rate可以网上找接口获取

> **提示：所以为了转账少花手续费，最好把utxo列表根据余额从大到小做个排序**

代码实现：
```java
    /***
     *
     * @param unSpentBTCList
     * @param value
     * @param rate sta/byte
     * @return -1发送的value超出了你的余额
     */
    public static long getFee(@NonNull List<UnSpentBTC> unSpentBTCList, long value, int rate) {
        long fee = 0L;
        int inputNum = 0;
        long totalMoney = 0;
        for (UnSpentBTC us : unSpentBTCList) {
            inputNum++;
            totalMoney += us.getSatoshis();
            if (totalMoney > value) {
                fee = (148 * inputNum + 34 * 1 + 10) * rate;
                if (totalMoney == (value + fee))
                    return fee;
                else if (totalMoney > (value + fee)) {
                    fee = (148 * inputNum + 34 * 2 + 10) * rate;
                    if (totalMoney >= (value + fee))
                        return fee;
                }
            }
        }
        return -1;
    }
```

有的朋友可能还会需要算出，最大能够发送的钱数，我这里也给一下代码，仅做参考：

```java
    /**
     * @param unSpentBTCList
     * @param totalMoney
     * @return
     */
    public static long getMaxSendValue(List<UnSpentBTC> unSpentBTCList, long totalMoney, int rate) {

        while (true) {
            long fee = getFee(unSpentBTCList, totalMoney, rate);
            if (fee == -1)
                totalMoney -= 100;
            else
                break;
        }
       /* long fee = getFee(unSpentBTCList, totalMoney, rate);
        if (fee == -1) {
            totalMoney -= 100;
            getMaxSendValue(unSpentBTCList, totalMoney, rate);
        }*/
        return totalMoney;
    }
```