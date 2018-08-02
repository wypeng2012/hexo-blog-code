---
title: 区块链开发之验证ETH地址的有效性
date: 2018-08-01 09:57:40
tags: 区块链
categories: 区块链
---
# 引言 #
之前介绍了eth私钥公钥地址的生成，接下来介绍一下eth地址有效性的校验，要做地址有效性的校验，首先要知道他的地址规则：

> eth的地址，就是把公钥用sha256hash之后，取其后160位生成的16进制字符串（40个字符）再加上前缀“0x”（总共42位）


# 校验流程 #
第一步,先判断地址非空和是否0x开头

第二步，把16进制字符串转成10进制数，看是否能否转换成功

第三部，判断是否长度是40位（去掉0x）

# 代码实现 #
```java
public static boolean isETHValidAddress(String input) {
        if (StringUtils.isNull(input) || !input.startsWith("0x"))
            return false;
        return WalletUtils.isValidAddress(input);
    }
```
isValidAddress方法

```java
    public static boolean isValidAddress(String input) {
        String cleanInput = Numeric.cleanHexPrefix(input);

        try {
            Numeric.toBigIntNoPrefix(cleanInput);
        } catch (NumberFormatException e) {
            return false;
        }

        return cleanInput.length() == ADDRESS_LENGTH_IN_HEX;
    }
```
