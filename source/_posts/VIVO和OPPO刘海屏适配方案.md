---
title: VIVO和OPPO刘海屏适配方案
date: 2018-07-13 11:32:23
tags: 
  - android刘海屏方案解决
  - android解决方案
categories: android刘海屏方案解决
---
# 引言： #
今年蓝绿厂，可谓是大放异彩啊，首先vivo 发布了全面屏手机NEX，开创了伸缩式前置摄像头的显赫，oppo紧跟着发布了find x，可为惊艳全球啊。当然在这之前蓝绿厂也发布了很多的流海异形屏手机，并且官方也给出了一些是配置指导，蓝绿厂的适配指导可以说是，没有说是没有适配，为何这样说呢，因为它没做啥特殊处理，不像华为和小米一样做了一些特殊处理，所以只要你的app之前做了[全面屏兼容处理](/2018/07/12/适配android全面屏/)，和沉浸式模式处理，就不必做其它特殊处理，但是这里，我还是要写一下他们官方提供的一些方法，有可能在做一些特殊处理的情况下会用得到。
# 1.判断是否刘海屏（凹凸屏） #
## （1）oppo判断方式 ##
```java
context.getPackageManager().hasSystemFeature(“com.oppo.feature.screen.heteromorphism”);//返回 true为凹形屏 ，可识别OPPO的手机是否为凹形屏。

```
## （2）vivo判断方式 ##
vivo不仅可以判断是否刘海屏，还可以判断时候有圆角，具体见以下代码：

```java
public static final int NOTCH_IN_SCREEN_VOIO_MARK = 0x00000020;//是否有凹槽
public static final int ROUNDED_IN_SCREEN_VOIO_MARK = 0x00000008;//是否有圆角
public static boolean isFeatureSupportInVIVO(Context context，int mark){
   boolean ret = false;
   try {
       ClassLoader cl = context.getClassLoader();
       Class ftFeature = cl.loadClass("android.util.FtFeature");
       Method get = ftFeature.getMethod("isFeatureSupport", int.class);
       ret = (boolean) get.invoke(ftFeature, mark);

   } catch (ClassNotFoundException e)
   { Log.e("test", "hasNotchInScreen ClassNotFoundException"); }
   catch (NoSuchMethodException e)
   { Log.e("test", "hasNotchInScreen NoSuchMethodException"); }
   catch (Exception e)
   { Log.e("test", "hasNotchInScreen Exception"); }
   finally
   { return ret; }
}

```
# 2.OPPO获取流海的详细信息 #
> 注:这个只有OPPO提供了，对应的方法
> 凹形屏坐标获取方法： 
> 获取ro.oppo.screen.heteromorphism属性值可获取凹形区域的范围，
> 例如 [ro.oppo.screen.heteromorphism]: [378,0:702,80]，含义如下 ：
> 
> 378：表示竖屏下左上角横坐标 
> 
> 0 ：表示竖屏下左上角竖坐标 
> 
> 702：表示竖屏下右下角横坐标
>  
> 80 ：表示竖屏下右下角竖坐标 

如下图：
![](/imgs/oppoliuhai.png)

```java
String mProperty = ""; 
mProperty = SystemProperties.get("ro.oppo.screen.heteromorphism"); 

/**
*反射系统方法封装类
调用方法：
String mProperty = ""; 
mProperty = SystemProperties.get("ro.oppo.screen.heteromorphism"); 
*/
public static class SystemProperties { 
       public static String get(String key) { 
              String value = ""; 
              Class<?> cls = null; 
              try { 
                   cls = Class.forName("android.os.SystemProperties"); 
                   Method hideMethod = cls.getMethod("get", String.class); 
                   Object object = cls.newInstance(); 
                   value = (String) hideMethod.invoke(object, key); 
            } catch (ClassNotFoundException e) { 
                  Log.e("error", "get error() ", e); 
            } catch (NoSuchMethodException e) { 
                  Log.e("error", "get error() ", e); 
            } catch (InstantiationException e) { 
                  Log.e("error", "get error() ", e); 
            } catch (IllegalAccessException e) { 
                  Log.e("error", "get error() ", e); 
            } catch (IllegalArgumentException e) { 
                  Log.e("error", "get error() ", e); 
            } catch (InvocationTargetException e) { 
                  Log.e("error", "get error() ", e); 
            } 
            return value; 
      } 
}

```
