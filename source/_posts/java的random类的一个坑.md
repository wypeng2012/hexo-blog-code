---
title: java的random类的一个坑（集合乱序）
date: 2018-07-20 16:30:56
tags:
 - java
 - 问题解决
categories: 问题解决
---
**引言**
在最近的开发中，要实现一个把一个集合的数给弄成乱序的，脑子里边的第一个想法就是使用随机数，先新建一个集合，写个死循环，然后获取随机位置下标，然后判断新集合是否包含这个数，不包含就把这个数加入新集合，包含，就继续下次循环，一直到新集合的长度达到和原始数组一样的长度为止，代码如下：

```java
 public List<String> randomList(List<String> words) {
        if (words == null)
            return null;
        List<String> list = new ArrayList<>();
        Random random = new Random();
        while (true) {
            int i = random.nextInt(words.size());
            // LogUtils.d("random:" + i);
            String s = words.get(i);
            if (list.size() == words.size())
                return list;
            if (list.contains(s))
                continue;
            else
                list.add(s);
        }

    }
```

这段大吗是没啥问题，刚开始也是，运行的很好，但是过了一段时间之后，发现有的手机会出问题：**这个死循环不会结束**

后来经过调试，发现，有时候随机数会出现某一个数，一直随机不出不来
，后来查阅文档发现，Random的nextInt(int n)方法有可能会出现问题：
> 该算法稍微有些复杂。它拒绝那些会导致不均匀分布的值（由于 2^31 无法被 n 整除）。某个值被拒绝的概率取决于 n。最坏的情况是 n=2^30+1，拒绝的概率是 1/2，循环终止前的预计迭代次数是 2。

这是倒霉催的遇上被拒绝的值了，所以应该不能一直nextInt(int n)同一个范围的值。

# 解决方案 #

使用JDK自带的方法，Collections的shuffle方法，他的实现方式是：
> public static void shuffle(List<?> list)
> 
> 使用默认随机源对指> 定列表进行置换。所有置换发生的可能性都是大致相等的。
> 前面描述中使用了不确定的词“大致”，因为随机源只是大致上独立选择位> 的无偏源。如果它是一个随机选择位的最佳源，那么算法将完全一致的选> 择置换。

> 此实现向后遍历列表，从最后一个元素一直到第二个元素，将随机选择的> 元素重复交换到“当前位置”。元素是从列表的一部分随机选择的，该部分> 列表从第一个元素一直到当前位置（包括）。

> 此方法以线性时间运行。如果指定列表没有实现 RandomAccess 接口并> 且是一个大型列表，则此实现在改组列表前将指定列表转储到数组中，并> 将改组后的数组转储回列表中。这避免了二次行为，该行为是原地改组一> 个“有序访问”列表引起的。 

源码如下：
```java
public static void shuffle(List<?> list, Random rnd) {
        int size = list.size();
        if (size < SHUFFLE_THRESHOLD || list instanceof RandomAccess) {
            for (int i=size; i>1; i--)
                swap(list, i-1, rnd.nextInt(i));
        } else {
            Object arr[] = list.toArray();

            // Shuffle array
            for (int i=size; i>1; i--)
                swap(arr, i-1, rnd.nextInt(i));

            // Dump array back into list
            // instead of using a raw type here, it's possible to capture
            // the wildcard but it will require a call to a supplementary
            // private method
            ListIterator it = list.listIterator();
            for (int i=0; i<arr.length; i++) {
                it.next();
                it.set(arr[i]);
            }
        }
    }
```

> 可以看出，它每次nextInt（）的随机源是不一样的，所以很好的解决了我碰到的那个问题

修改后的方法代码如下：
```java
   public List<String> randomList(List<String> words) {
        if (words == null)
            return null;
        List<String> list = new ArrayList<>();
        list.addAll(words);
        Collections.shuffle(list);
        return list;
    }
```
