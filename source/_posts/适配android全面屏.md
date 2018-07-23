---
title: 适配android全面屏
date: 2018-07-12 15:34:44
tags:
  - android适配
  - android解决方案
categories: android问题解决
---
# 引言： #
随着android手机的发展，手机屏幕已经从4:3,16:9,16:10发展到现如今的所谓的全面屏，比例一般为18:9，18.9:9,19:9。
# 更大的屏幕高宽比： #
以三星S8为例，屏幕比例为18.5:9，从下图可以看到，在 1080P 的分辨率下，比标准的 16:9 屏幕，足足多了300像素。开发者需要作一些优化，以充分利用更大的显示空间。
![](/imgs/s8_display.png)

这就会对一些app存在兼容性问题比如不能全屏显示啊等等，如下图所示：
![今日头条在S8的效果](/imgs/今日头条在S8的效果.png)

# 解决方式： #
## 1.声明 Maximum Aspect Ratio ##
Android 标准接口中，支持应用声明其支持的最大屏幕高宽比（maximum aspect ratio）。具体声明如下，其中的 ratio_float 被定义为是高除以宽，以 16:9 为例，ratio_float = 16/9 = 1.778 (18.5:9则为2.056)。

```java
<application>
    <meta-data android:name="android.max_aspect" android:value="ratio_float" />
</application>

```

若开发者没有声明该属性，ratio_float 的默认值为1.86，小于2.056，因此这类应用在三星S8上，默认不会全屏显示，屏幕两边会留黑，如上图所示
> 因此建议开发者声明 Maximum Aspect Ratio 2 或更多。值得一提的是，如果应用的 **android:resizeableActivity** 已经设置为 **true**，就不必设置 **Maximum Aspect Ratio** 了。**还有一点就是：如果您的应用面向 Android N，但未对android:resizeableActivity属性指定值，则该属性的值默认设为 true。**

## 2.避免内容拉伸/变形 ##
从16:9变成18.5:9，图片往往被会拉伸变形，此问题常见于开屏素材。开发者应使用更灵活的布局，以适应不同的屏幕比例。

> 接下来我会出一系列的文章，说一下几种国产手机的刘海屏适配方案和google官方的适配方案


