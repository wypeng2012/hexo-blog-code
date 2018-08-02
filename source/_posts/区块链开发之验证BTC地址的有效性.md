---
title: 区块链开发之验证BTC地址的有效性
date: 2018-08-01 09:38:35
tags: 区块链
categories: 区块链
---
# 引言 #
之前介绍了btc私钥公钥地址的生成，接下来介绍一下btc地址有效性的校验，要做地址有效性的校验，首先要知道他的地址生成规则：

第一步，随机选取一个32字节的数、大小介于1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141之间，作为私钥。
18E14A7B6A307F426A94F8114701E7C8E774E7F9A47E2C2035DB29A206321725

第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。 (共65字节， 1字节 0x04, 32字节为x坐标，32字节为y坐标）关于公钥压缩、非压缩的问题另文说明。

0450863AD64A87AE8A2FE83C1AF1A8403CB53F53E486D8511DAD8A04887E5B
23522CD470243453A299FA9E77237716103ABC11A1DF38855ED6F2EE187E9C582BA6

第三步，计算公钥的 SHA-256 哈希值
600FFE422B4E00731A59557A5CCA46CC183944191006324A447BDB2D98D4B408

第四步，取上一步结果，计算 RIPEMD-160 哈希值
010966776006953D5567439E5E39F86A0D273BEE

第五步，取上一步结果，前面加入地址版本号（比特币主网版本号“0x00”）
00010966776006953D5567439E5E39F86A0D273BEE

第六步，取上一步结果，计算 SHA-256 哈希值
445C7A8007A93D8733188288BB320A8FE2DEBD2AE1B47F0F50BC10BAE845C094

第七步，取上一步结果，再计算一下 SHA-256 哈希值（哈哈）
D61967F63C7DD183914A4AE452C9F6AD5D462CE3D277798075B107615C1A8A30

第八步，取上一步结果的前4个字节（8位十六进制）
D61967F6

第九步，把这4个字节加在第五步的结果后面，作为校验（这就是比特币地址的16进制形态）。
00010966776006953D5567439E5E39F86A0D273BEED61967F6

第十步，用base58表示法变换一下地址（这就是最常见的比特币地址形态）。
16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM

# 校验流程 #
知道了生成流程，就好实现校验方法了。

第一步，先把地址base58解码成字节数组，然后把数组分成两个字节数组，一个是后4字节数组（字节数组1），一个是减去后4字节的数组（字节数组2），然后把字节数组2两次Sha256Hash，然后取其前4位，跟字节数组1比较，是相同的，就校验通过。

第二步，把第一步校验通过的解码字节数组取第一个字节&0xff，得到版本号，然后检验版本号的合法性（这个是根据主网参数校验的）

# 代码实现 #
这些代码bitcoinj里面已经有对应方法了，我们只需要封装一下就可以了，代码如下：
```java
    public static boolean isBTCValidAddress(String input) {
        try {
            NetworkParameters networkParameters = null;
            if (!BTC_TEST_NET)
                networkParameters = MainNetParams.get();
            else
                networkParameters = TestNet3Params.get();
            Address address = Address.fromBase58(networkParameters, input);
            if (address != null)
                return true;
            else
                return false;
        } catch (Exception e) {
            return false;
        }
    }
```