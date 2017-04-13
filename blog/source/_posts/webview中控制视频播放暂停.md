---
title: webview中控制视频播放暂停
date: 2017-04-13 21:50:04
tags:
- 笔记
categories:
- 笔记
- android
---
## 视频播放失败情况

webview中https和http混合使用访问导致访问失败
在低版本中是可以混合使用的，高版本中默认取消了这个模式需要手动开启
```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
          webView.getSettings().setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
  }
```

## 播放视频退出声音还在

webview中播放视频需要手动控制停止还是播放可以通过反射方法来控制
### 播放暂停
```java
webView.getClass().getMethod("onPause").invoke(webView,(Object[])null);
```
### 播放开始
```java
webView.getClass().getMethod("onResume").invoke(webView,(Object[])null);
```
