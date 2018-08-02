---
title: 区块链开发之确定性算法生成BTC，ETH的私钥，公钥和地址
date: 2018-07-30 19:21:08
tags: 区块链
categories: 区块链
---
# 引言 #
首先要生成12个助记词，请看我上篇文章：[区块链开发之生成12个助记词](/2018/07/30/区块链开发之生成12个助记词/)

# 使用的库 #
> implementation ‘org.bitcoinj:bitcoinj-core:0.14.7’
> 
> implementation 'org.web3j:core:3.3.1-android'


# 具体代码如下 #
```java
public static ShellWallet generateWallet(int coinType, int index, String name) throws Exception {
        ShellWallet wallet = null;
        if (words == null || words.size() != 12) {
            throw new RuntimeException("please generateMnemonic first");
        } else {
            DeterministicSeed deterministicSeed = new DeterministicSeed(words, null, "", 0);
            DeterministicKeyChain deterministicKeyChain = DeterministicKeyChain.builder().seed(deterministicSeed).build();
            //拼接路径 path = "m/44'/60'/0'/0/0"
            String path = null;
            if (coinType == BTC_COIN) {
                path = "m/44'/0'/0'/0/" + index;
                NetworkParameters networkParameters = null;
                if (!BTC_TEST_NET) {
                    networkParameters = MainNetParams.get();
                } else {
                    networkParameters = TestNet3Params.get();
                    path = "m/44'/1'/0'/0/" + index;
                }
                BigInteger privkeybtc = deterministicKeyChain.getKeyByPath(parsePath(path), true).getPrivKey();
                ECKey ecKey = ECKey.fromPrivate(privkeybtc);

                //String publicKey = Numeric.toHexStringNoPrefixZeroPadded(new BigInteger(ecKey.getPubKey()), 66);
                //LogUtils.d("pub1:" + publicKey );
                String publicKey = ecKey.getPublicKeyAsHex();
                LogUtils.d("pub2:" + publicKey);
                String privateKey = ecKey.getPrivateKeyEncoded(networkParameters).toString();
                String address = ecKey.toAddress(networkParameters).toString();

                wallet = new ShellWallet(index, coinType, privateKey, publicKey, address, name);
                LogUtils.d(wallet.toString());
            } else if (coinType == ETH_COIN) {
                path = "m/44'/60'/0'/0/" + index;
                BigInteger privkeyeth = deterministicKeyChain.getKeyByPath(parsePath(path), true).getPrivKey();
                ECKeyPair ecKeyPair = ECKeyPair.create(privkeyeth);

                String publicKey = Numeric.toHexStringWithPrefix(ecKeyPair.getPublicKey());
                String privateKey = Numeric.toHexStringWithPrefix(ecKeyPair.getPrivateKey());
                String address = "0x" + Keys.getAddress(ecKeyPair);

                wallet = new ShellWallet(index, coinType, privateKey, publicKey, address, name);
                LogUtils.d(wallet.toString());
            }

        }

        return wallet;
    }
```

```java
/**
     * The path is a human-friendly representation of the deterministic path. For example:
     * <p>
     * "m/44'/0'/0'/0/0"
     * <p>
     * Where a letter "'" means hardened key. Spaces are ignored.
     */
    public static List<ChildNumber> parsePath(@Nonnull String path) {
        String[] parsedNodes = path.replace("m", "").split("/");
        List<ChildNumber> nodes = new ArrayList<>();

        for (String n : parsedNodes) {
            n = n.replaceAll(" ", "");
            if (n.length() == 0) continue;
            boolean isHard = n.endsWith("'");
            if (isHard) n = n.substring(0, n.length() - 1);
            int nodeNumber = Integer.parseInt(n);
            nodes.add(new ChildNumber(nodeNumber, isHard));
        }

        return nodes;
    }
```