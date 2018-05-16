---
title: Android WebView 缓存
date: 2018/5/11 14:26:41
tags:
categories: [编程, Android, WebView]
---

这篇文章介绍的相当详细
https://mp.weixin.qq.com/s?__biz=MzIzOTU0NTQ0MA==&mid=2247486880&idx=1&sn=23a99bfbb84a4d81a93ef077648c23d8&chksm=e92930afde5eb9b9f50197f2b057b43d5bbeb0d427d7350b1753d3104d5f80799d98be2fb674&mpshare=1&scene=1&srcid=0206HJ59SRcsUfY1FLL3PZxI&key=bbabbd357fc2e77d69e8ff8a4b2a82b324539f84bdc13a99c3e90810680a84c2554e2ac495b8a21bd6e28f9bc8c3ce6276058c24dc83fd21de57bec8d0fe0516201bfa79d565e1c54ac10919bf5cbd0d&ascene=0&uin=MjIyMDg1MTcwMQ%3D%3D&devicetype=iMac+MacBookAir7%2C2+OSX+OSX+10.11.6+build(15G31)&version=12010310&nettype=WIFI&lang=zh_CN&fontScale=100&pass_ticket=skJyHUEc3cbp%2FP1ftxfAouU1lLnGFduzq0f2%2BFTZg3KgD02TngGH5FA2WdaKHPPN


如果采用默认的cache设置，WebView 会直接读取缓存而不会去请求，说明WebSettings.LOAD_DEFAULT这个模式是根据cache-control决定是否从网络上获取数据。
同时无网络时他会请求失败。

首次请求：200

缓存有效期内请求：200(from cache)

缓存过期后请求：304（Not Modified)

一般浏览器会将缓存记录及缓存文件存在本地 Cache 文件夹中。Android 下 App 如果使用 Webview，缓存的文件记录及文件内容会存在当前 app 的 data 目录中。
分析：Cache-Control 和 Last-Modified 一般用在 Web 的静态资源文件上，如 JS、CSS 和一些图像文件。通过设置资源文件缓存属性，对提高资源文件加载速度，节省流量很有意义，特别是移动网络环境。但问题是：缓存有效时长该如何设置？如果设置太短，就起不到缓存的使用；如果设置的太长，在资源文件有更新时，浏览器如果有缓存，则不能及时取到最新的文件。
Last-Modified 需要向服务器发起查询请求，才能知道资源文件有没有更新。虽然服务器可能返回304告诉没有更新，但也还有一个请求的过程。对于移动网络，这个请求可能是比较耗时的。有一种说法叫“消灭304”，指的就是优化掉304的请求。
抓包发现，带 if-Modified-Since 字段的请求，如果服务器回包304，回包带有 Cache-Control:max-age 或 Expires 字段，文件的缓存有效时间会更新，就是文件的缓存会重新有效。304回包后如果再请求，则又直接使用缓存文件了，不再向服务器查询文件是否更新了，除非新的缓存时间再次过期。
另外，Cache-Control 与 Last-Modified 是浏览器内核的机制，一般都是标准的实现，不能更改或设置。以 QQ 浏览器的 X5为例，Cache-Control 与 Last-Modified 缓存不能禁用。缓存容量是12MB，不分HOST，过期的缓存会最先被清除。如果都没过期，应该优先清最早的缓存或最快到期的或文件大小最大的；过期缓存也有可能还是有效的，清除缓存会导致资源文件的重新拉取。
还有，浏览器，如 X5，在使用缓存文件时，是没有对缓存文件内容进行校验的，这样缓存文件内容被修改的可能。
分析发现，浏览器的缓存机制还不是非常完美的缓存机制。完美的缓存机制应该是这样的：

缓存文件没更新，尽可能使用缓存，不用和服务器交互；
缓存文件有更新时，第一时间能使用到新的文件；
缓存的文件要保持完整性，不使用被修改过的缓存文件；
缓存的容量大小要能设置或控制，缓存文件不能因为存储空间限制或过期被清除。
以X5为例，第1、2条不能同时满足，第3、4条都不能满足。
在实际应用中，为了解决 Cache-Control 缓存时长不好设置的问题，以及为了”消灭304“，Web前端采用的方式是：
在要缓存的资源文件名中加上版本号或文件 MD5值字串，如 common.d5d02a02.js，common.v1.js，同时设置 Cache-Control:max-age=31536000，也就是一年。在一年时间内，资源文件如果本地有缓存，就会使用缓存；也就不会有304的回包。
如果资源文件有修改，则更新文件内容，同时修改资源文件名，如 common.v2.js，html页面也会引用新的资源文件名。
通过这种方式，实现了：缓存文件没有更新，则使用缓存；缓存文件有更新，则第一时间使用最新文件的目的。即上面说的第1、2条。第3、4条由于浏览器内部机制，目前还无法满足。


https://blog.csdn.net/lx448593jp/article/details/50503762?locationNum=12
