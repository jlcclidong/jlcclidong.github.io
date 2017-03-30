---
title: for与foreach在List中的使用
date: 2017-03-30 10:24:15
tags:
- 笔记
- java
categories:
- 笔记
- java
---

# for()循环与foreach()在List中的使用

## 数组
对于数组中两种循环都是相同的 foreach转换成不同for循环

## List

对于List中foreach相当于使用了Iterator遍历

而List中有的实现了RandomAccess()的方法 而在RandomAccess()中明确说明
```java

*     for (int i=0, n=list.size(); i &lt; n; i++)
*         list.get(i);

* runs faster than this loop:

*     for (Iterator i=list.iterator(); i.hasNext(); )
*         i.next();

```
List中ArrayList和Vector中实现了RandomAccess接口 LinkedList并没有

所里在ArrayList中使用普通For循环速度会比增强for快
