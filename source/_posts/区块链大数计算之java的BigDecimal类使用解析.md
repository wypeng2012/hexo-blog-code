---
title: 区块链大数计算之java的BigDecimal类使用解析
date: 2018-07-24 13:32:22
tags:
  - java
  - 大数计算
categories: 区块链
---
之前讲解了BigInteger类的使用，这个类是做大整数运算的，想做大的浮点数运算就不行了，所以就需要其它的类来解决浮点运算的问题，这个类就是BigDecimal。

# BigDecimal介绍 #
不可变的、任意精度的有符号十进制数。BigDecimal 由任意精度的整数非标度值 和 32 位的整数标度 (scale) 组成。如果为零或正数，则标度是小数点后的位数。如果为负数，则将该数的非标度值乘以 10 的负 scale 次幂。因此，BigDecimal 表示的数值是 (unscaledValue × 10-scale)。 

BigDecimal 类提供以下操作：算术、标度操作、舍入、比较、哈希算法和格式转换。toString() 方法提供 BigDecimal 的规范表示形式。 

BigDecimal 类使用户能完全控制舍入行为。如果未指定舍入模式，并且无法表示准确结果，则抛出一个异常；否则，通过向该操作提供适当的 MathContext 对象，可以对已选择的精度和舍入模式执行计算。在任何情况下，可以为舍入控制提供八种舍入模式。使用此类（例如，ROUND_HALF_UP）中的整数字段来表示舍入模式已过时；应改为使用 RoundingMode enum（例如，RoundingMode.HALF_UP）的枚举值。 

当为 MathContext 对象提供 0 的精度设置（例如，MathContext.UNLIMITED）时，算术运算是准确的，它们是不采用任何 MathContext 对象的算术方法。（这是第 5 版之前的版本支持的唯一行为。）为了计算准确结果，不使用附带 0 精度设置的 MathContext 对象的舍入模式设置，因此与该对象无关。在除法中，准确的商可能是一个无限长的十进制扩展；例如，1 除以 3 所得的商。如果商具有无穷的十进制扩展，但是指定了该操作返回准确结果，则抛出 ArithmeticException。否则，像其他操作那样，返回除法运算的准确结果。 

当精度设置不为 0 时，BigDecimal 算法的规则完全符合 ANSI X3.274-1996 和 ANSI X3.274-1996/AM 1-2000（ 7.4 节）中定义的算法的可选操作模式。与上述标准不同，BigDecimal 包括多种舍入模式，它们对于版本 5 以前的 BigDecimal 版本中的除法是强制性的。这些 ANSI 标准和 BigDecimal 规范之间的任何冲突都按照有利于 BigDecimal 的方式进行解决。 

由于同一数值可以有不同的表示形式（具有不同的标度），因此运算和舍入的规则必须同时指定数值结果和结果表示形式中所用的标度。 

一般情况下，当准确结果（在除法中，可能有无限多位）比返回的数值具有更多位数时，舍入模式和精度设置确定操作如何返回具有有限位数的结果。 首先，MathContext 的 precision 设置指定要返回的总位数；这确定了结果的精度。位数计数从准确结果的最左边的非零数字开始。舍入模式确定丢弃的尾部位数如何影响返回的结果。 

对于所有算术运算符，运算的执行方式是，首先计算准确的中间结果，然后，使用选择的舍入模式将其舍入为精度设置（如有必要）指定的位数。如果不返回准确结果，则将丢弃准确结果的某些数位。当舍入增加了返回结果的大小时，前导数字“9”的进位传播可能会创建新的数位。例如，将值 999.9 舍入为三位数字，则在数值上等于一千，表示为 100×101。在这种情况下，新的 "1" 是返回结果的前导数位。 

除了逻辑的准确结果外，每种算术运算都有一个表示结果的首选标度。下表列出了每个运算的首选标度。 

> 当为任何输入参数传递 null 对象引用时，此类的所有方法和构造方法都将抛出 NullPointerException。 

# 字段摘要 #

| 类型 | 变量名 |
| ------ | ------ |
| static BigDecimal | **ONE**   值为 1，标度为 0。 |
| static int | **ROUND_CEILING**   接近正无穷大的舍入模式。如果 BigDecimal 为正，则舍入行为与 ROUND_UP 相同；如果为负，则舍入行为与 ROUND_DOWN 相同。注意，此舍入模式始终不会减少计算值。 |
| static int | **ROUND_DOWN**    接近零的舍入模式。在丢弃某部分之前始终不增加数字（即截短）。注意，此舍入模式始终不会增加计算值的大小。 |
| static int | **RROUND_FLOOR** 接近负无穷大的舍入模式。如果 BigDecimal 为正，则舍入行为与 ROUND_DOWN 相同；如果为负，则舍入行为与 ROUND_UP 相同。注意，此舍入模式始终不会增加计算值。 |
| static int | **ROUND_HALF_DOWN** 向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为上舍入的舍入模式。如果舍弃部分 > 0.5，则舍入行为与 ROUND_UP 相同；否则舍入行为与 ROUND_DOWN 相同。 |
| static int | **ROUND_HALF_EVEN** 向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则向相邻的偶数舍入。如果舍弃部分左边的数字为奇数，则舍入行为与 ROUND_HALF_UP 相同；如果为偶数，则舍入行为与 ROUND_HALF_DOWN 相同。注意，在重复进行一系列计算时，此舍入模式可以将累加错误减到最小。 |
| static int | **ROUND_HALF_UP** 向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为向上舍入的舍入模式。如果舍弃部分 >= 0.5，则舍入行为与 ROUND_UP 相同；否则舍入行为与 ROUND_DOWN 相同。注意，这是我们大多数人在小学时就学过的舍入模式。 |
| static int | **ROUND_UNNECESSARY** 断言请求的操作具有精确的结果，因此不需要舍入。如果对获得精确结果的操作指定此舍入模式，则抛出 ArithmeticException。 |
| static int | **ROUND_UP** 舍入远离零的舍入模式。在丢弃非零部分之前始终增加数字。注意，此舍入模式始终不会减少计算值的大小。 |
| static BigDecimal | **TEN** 值为 10，标度为 0。 |
| static BigDecimal | **ZERO** 值为 0，标度为 0。 |

# 常用构造方法  #

| 构造方法 | 解释 |
| ------ | ------ |
| public BigDecimal(String val) | 将 BigDecimal 的字符串表示形式转换为 BigDecimal。字符串表示形式由可选符号'+' ('\u002B') 或 '-' ('\u002D') 组成，后跟零或多个十进制数字（“整数”）的序列，可以选择后跟一个小数，也可以选择后跟一个指数。该小数由小数点以及后跟的零或更多十进制数字组成。字符串必须至少包含整数或小数部分中的一个数字。由符号、整数和小数部分组成的数字称为有效位数。 |
| public BigDecimal(String val, MathContext mc) | 将 BigDecimal 的字符串表示形式转换为 BigDecimal，接受与 BigDecimal(String) 构造方法相同的字符串（按照上下文设置进行舍入）。 |
| public BigDecimal(double val) | 将 double 转换为 BigDecimal，后者是 double 的二进制浮点值准确的十进制表示形式。返回的 BigDecimal 的标度是使 (10scale × val) 为整数的最小值。 |
| public BigDecimal(BigInteger val) | 将 BigInteger 转换为 BigDecimal。BigDecimal 的标度是零 |
| public BigDecimal(int val) | 将 int 转换为 BigDecimal。BigDecimal 的标度为零。 |
| public BigDecimal(long  val) | 将 long  转换为 BigDecimal。BigDecimal 的标度为零。 |
| public BigDecimal(BigInteger unscaledVal,int scale) |将 BigInteger 非标度值和 int 标度转换为 BigDecimal。BigDecimal 的值为 (unscaledVal × 10-scale)。  |

# 常用方法 #

| 方法 | 解释 |
| ------ | ------ |
| public static BigDecimal valueOf(long unscaledVal,int scale) | 将 long 非标度值和 int 标度转换为 BigDecimal。提供的此“静态工厂方法”优先于 (long, int) 构造方法，因为前者允许重用经常使用的 BigDecimal 值。  |
| public static BigDecimal valueOf(long val) | 将 long 值转换为具有零标度的 BigDecimal。提供的此“静态工厂方法”优先于 (long) 构造方法，因为前者允许重用经常使用的 BigDecimal 值。 |
| public static BigDecimal valueOf(double val) | 使用 Double.toString(double) 方法提供的 double 规范的字符串表示形式将 double 转换为 BigDecimal。 注：这通常是将 double（或 float）转化为 BigDecimal 的首选方法，因为返回的值等于从构造 BigDecimal（使用 Double.toString(double) 得到的结果）得到的值。 |
| public BigDecimal add(BigDecimal augend) | 返回一个 BigDecimal，其值为 (this + augend)，其标度为 max(this.scale(), augend.scale())。 |
| public BigDecimal subtract(BigDecimal subtrahend) | 返回一个 BigDecimal，其值为 (this - subtrahend)，其标度为 max(this.scale(), subtrahend.scale())。 |
| public BigDecimal multiply(BigDecimal multiplicand) | 返回一个 BigDecimal，其值为 (this × multiplicand)，其标度为 (this.scale() + multiplicand.scale())。  |
| public BigDecimal divide(BigDecimal divisor) | 返回一个 BigDecimal，其值为 (this / divisor)，其首选标度为 (this.scale() - divisor.scale())；如果无法表示准确的商值（因为它有无穷的十进制扩展），则抛出 ArithmeticException。 |
| public BigDecimal remainder(BigDecimal divisor) | 返回其值为 (this % divisor) 的 BigDecimal。 余数由 this.subtract(this.divideToIntegralValue(divisor).multiply(divisor)) 给出。注意，这不是模操作（结果可以为负）。 |
| public BigDecimal pow(int n) | 返回其值为 (thisn) 的 BigDecimal，准确计算该幂，使其具有无限精度。 参数 n 必须在 0 到 999999999（包括）之间。ZERO.pow(0) 返回 ONE。 注意，未来版本可能会扩展此方法允许的指数范围。 |
| public BigDecimal abs() | 返回 BigDecimal，其值为此 BigDecimal 的绝对值，其标度为 this.scale()。  |
| public BigDecimal plus() | 返回 BigDecimal，其值为 (+this)，其标度为 this.scale()。 |
| public BigDecimal setScale(int newScale, RoundingMode roundingMode) | 返回 BigDecimal，其标度为指定值，其非标度值通过此 BigDecimal 的非标度值乘以或除以十的适当次幂来确定，以维护其总值。如果该操作减少标度，则非标度值必须被除（而不是乘），并且该值可以更改；在这种情况下，将指定的舍入模式应用到除法中。 参数：newScale - 要返回的 BigDecimal 值的标度。roundingMode - 要应用的舍入模式。 |
| public int compareTo(BigDecimal val) | 当此 BigDecimal 在数字上小于、等于或大于 val 时，返回 -1、0 或 1。 |
| public String toPlainString() | 返回不带指数字段的此 BigDecimal 的字符串表示形式。对于具有正标度的值，小数点右边的数字个数用于指示标度。对于具有零或负标度的值，生成得到的字符串，好像将该值转换为在数值上等于具有零标度的值一样，并且好像零标度值的所有尾部零都出现在该结果中。 如果非标度值小于零，则整个字符串以减号 '-' ('\u002D') 为前缀。如果非标度值为零或正数，则没有任何符号字符作为前缀。 注意，如果将此方法的结果传递到 string constructor，则只需要恢复此 BigDecimal 的数值；新的 BigDecimal 的表示形式可以有不同的标度。尤其是，如果此 BigDecimal 具有负标度，则在由字符串构造方法进行处理时，此方法产生的字符串将具有零标度。 （此方法的作用类似于 1.4 和更早版本中的 toString 方法。）  |
| public String toString() | 返回此 BigDecimal 的字符串表示形式，如果需要指数，则使用科学记数法。 |
| public String toEngineeringString() | 返回此 BigDecimal 的字符串表示形式，需要指数时，则使用工程计数法。 |