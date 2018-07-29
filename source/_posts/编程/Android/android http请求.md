---
title: android http 请求
date: 2018/6/26 9:26:41
tags:
categories: [Android, http]
---

[Android]HttpClient和HttpURLConnection的区别:
在Android 2.2版本之前，HttpClient拥有较少的bug，因此使用它是最好的选择。
而在Android 2.3版本及以后，HttpURLConnection则是最佳的选择。它的API简单，体积较小，因而非常适用于Android项目。压缩和缓存机制可以有效地减少网络访问的流量，在提升速度和省电方面也起到了较大的作用。对于新的应用程序应该更加偏向于使用HttpURLConnection，因为在以后的工作当中我们也会将更多的时间放在优化HttpURLConnection上面。
https://www.cnblogs.com/zhousysu/p/5483896.html
