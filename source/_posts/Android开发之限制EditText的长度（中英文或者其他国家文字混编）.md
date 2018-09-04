---
title: Android开发之限制EditText的长度（中英文或者其他国家文字混编）
date: 2018-08-07 16:59:25
tags:
  - Android开发
  - Android问题解决
categories: Android问题解决
---
# 引言 #
在开发中难免会碰到限制EditText长的的需求，最简单的做法就是设置maxLength属性，但是这个是不准确的，他的计算方式是按照字符数，算的，但是现实使用中，同样字符长度的汉字是比英文或者字符要长的，所以要按照是否中文和英文或者混排，来限制长度，一般汉字按照两个来计算的。Android里边使用自定义InputFilter来实现，具体代码如下：

```java
public class CustomCoinNameFilter implements InputFilter {

    private int maxLength;//最大长度，ASCII码算一个，其它算两个

    public CustomCoinNameFilter(int maxLength) {
        this.maxLength = maxLength;
    }

    @Override
    public CharSequence filter(CharSequence source, int start, int end, Spanned dest, int dstart, int dend) {
        if (TextUtils.isEmpty(source)) {
            return null;
        }
        LogUtils.e("source:" + source);
        LogUtils.e("dest:" + dest);
        int inputCount = 0;
        int destCount = 0;
        inputCount = getCurLength(source);
        LogUtils.e("inputCount:" + inputCount);
        if (dest.length() != 0)
            destCount = getCurLength(dest);
        LogUtils.e("destCount:" + destCount);
        if (destCount >= maxLength)
            return "";
        else {

            int count = inputCount + destCount;
            if (dest.length() == 0) {
                if (count <= maxLength)
                    return null;
                else
                    return sub(source, maxLength);
            }
            LogUtils.e("count:" + count);
            if (count > maxLength) {
                //int min = count - maxLength;
                int maxSubLength = maxLength - destCount;
                return sub(source, maxSubLength);
            }
        }
        return null;
    }

    private CharSequence sub(CharSequence sq, int subLength) {
        int needLength = 0;
        int length = 0;
        for (int i = 0; i < sq.length(); i++) {
            if (sq.charAt(i) < 128)
                length += 1;
            else
                length += 2;
            ++needLength;
            if (subLength <= length) {
                return sq.subSequence(0, needLength);
            }
        }
        return sq;
    }

    private int getCurLength(CharSequence s) {
        int length = 0;
        if (s == null)
            return length;
        else {
            for (int i = 0; i < s.length(); i++) {
                if (s.charAt(i) < 128)
                    length += 1;
                else
                    length += 2;
            }
        }
        return length;
    }
}

```

# 使用 #
```java
mSetWalletName.setFilters(new InputFilter[]{new CustomCoinNameFilter(20)});
```