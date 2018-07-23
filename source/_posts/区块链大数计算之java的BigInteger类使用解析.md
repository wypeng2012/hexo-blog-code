---
title: 区块链大数计算之java的BigInteger类使用解析
date: 2018-07-19 19:02:37
tags:
  - java
  - 大数计算
categories: 区块链
---
# 引言 #
区块链里面用的最多的就是计算，并且还是很大的数的计算。大家都知道java里面基本类型里面就long类型可以表示一个64位的大数字，其数值范围为：


>  long的最大值：9223372036854775807
>  
>  long的最小值：-9223372036854775808


这个数，看起来很大了，但是对于区块链来说完全是不够的，大家可以看一下我之前的一篇文章：[区块链开发以太坊ETH单位转换关系](/2018/07/19/区块链开发之以太坊ETH单位转换关系/)

这里边的数值都很大，做运算时，绝对不可能使用基本类型来做运算的。还好jdk里面已经内置了相关的类来解决这些问题，那就是BigInteger（主要做整数运算）和BigDecimal（主要做浮点运算），今天我们就来介绍一下BigInteger的用法（后边会写和BigDecimal的用法）：

# 常用构造方法 #

```java
BigInteger(String val)  //将 BigInteger 的十进制字符串表示形式转换为 BigInteger。
          
BigInteger(String val, int radix) //将指定基数的 BigInteger 的字符串表示形式转换为 BigInteger。 radix只得是进制，比如2进制，就填写2,16进制，就填16

```
# 常用方法 #
## 1.求绝对值 ##
```java
abs()//返回其值是此 BigInteger 的绝对值的 BigInteger。

```
## 2.加法 ##
```java
add(BigInteger val) //相当于做加法，返回其值为 (this + val) 的 BigInteger。

```
## 3.比较大小 ##
```java
compareTo(BigInteger val)//比较大小，大于返回1，等于返回0，小于返回-1

```
## 4.除法 ##
```java
divide(BigInteger val)//相当于做除法 返回其值为 (this / val) 的 BigInteger。

```
## 5.转成double ##
```java
doubleValue()//将此 BigInteger 转换为 double。如果此 BigInteger 的数量太大，不能表示为 double，则将其适当地转换为 Double.NEGATIVE_INFINITY 或 Double.POSITIVE_INFINITY。注意，即使在返回值是有限的情况下，此转换也可以丢失关于 BigInteger 值的精度的信息。

```
## 6.转换为 float ##
```java
floatValue() //将此 BigInteger 转换为 float。如果此 BigInteger 的数量太大，不能表示为 float，则将其适当地转换为 Float.NEGATIVE_INFINITY 或 Float.POSITIVE_INFINITY。注意，即使在返回值是有限的情况下，此转换也可以丢失关于 BigInteger 值的精度的信息。 

```
## 7.转换为 int ##
```java
intValue() //将此 BigInteger 转换为 int。如果此 BigInteger 太长而不适合用 int 表示，则仅返回 32 位的低位字节。注意，此转换会丢失关于该 BigInteger 值的总大小的信息，并返回带有相反符号的结果

```
## 7.转换为 long ##
```java
longValue() //将此 BigInteger 转换为 long。如果此 BigInteger 太长而不适合用 long 表示，则仅返回 64 位的低位字节。注意，此转换会丢失关于该 BigInteger 值的总大小的信息，并返回带有相反符号的结果。 

```
## 8.乘法 ##
```java
multiply(BigInteger val) //返回其值为 (this * val) 的 BigInteger。
```
## 9.减法 ##
```java
subtract(BigInteger val)//返回其值为 (this - val) 的 BigInteger。
```
## 10.转成十进制字符串表示形式字符串 ##
```java
toString() //返回此 BigInteger 的十进制字符串表示形式。
```
## 11.转成2,8,10,16进制字符串表示形式字符串 ##
```java
toString(int radix)//返回此 BigInteger 的给定基数的字符串表示形式。radix为进制数
```

> Tip:其他方法可以查看帮助文档