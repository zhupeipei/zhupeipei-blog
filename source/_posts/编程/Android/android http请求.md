---
title: android http 请求
date: 2018/6/26 9:26:41
tags:
categories: [Android, http]
---

#### HttpClient和HttpURLConnection的区别:
在Android 2.2版本之前，HttpClient拥有较少的bug，因此使用它是最好的选择。
而在Android 2.3版本及以后，HttpURLConnection则是最佳的选择。它的API简单，体积较小，因而非常适用于Android项目。压缩和缓存机制可以有效地减少网络访问的流量，在提升速度和省电方面也起到了较大的作用。对于新的应用程序应该更加偏向于使用HttpURLConnection，因为在以后的工作当中我们也会将更多的时间放在优化HttpURLConnection上面。
#### 1. HttpClient
DefaultHttpClient和它的兄弟AndroidHttpClient都是HttpClient具体的实现类，它们都拥有众多的API，而且实现比较稳定，bug数量也很少。
但同时也由于HttpClient的API数量过多，使得我们很难在不破坏兼容性的情况下对它进行升级和扩展，所以目前Android团队在提升和优化HttpClient方面的工作态度并不积极。
#### 2. HttpURLConnection
HttpURLConnection是一种多用途、轻量极的HTTP客户端，使用它来进行HTTP操作可以适用于大多数的应用程序。虽然HttpURLConnection的API提供的比较简单，但是同时这也使得我们可以更加容易地去使用和扩展它。
不过在Android 2.2版本之前，HttpURLConnection一直存在着一些令人厌烦的bug。比如说对一个可读的InputStream调用close()方法时，就有可能会导致连接池失效了。那么我们通常的解决办法就是直接禁用掉连接池的功能：

```
private void disableConnectionReuseIfNecessary() {  
    // 这是一个2.2版本之前的bug  
    if (Integer.parseInt(Build.VERSION.SDK) < Build.VERSION_CODES.FROYO) {  
        System.setProperty("http.keepAlive", "false");  
    }  
}
```
在Android 2.3版本的时候，我们加入了更加透明化的响应压缩。HttpURLConnection会自动在每个发出的请求中加入如下消息头，并处理相应的返回结果：
```
Accept-Encoding: gzip
```

配置你的Web服务器来支持对客户端的响应进行压缩的功能，从而可以在这一改进上获取到最大的好处。如果在压缩响应的时候出现了问题，这篇文档会告诉你如何禁用掉这个功能。

但是如果启动了响应压缩的功能，HTTP响应头里的Content-Length就会代表着压缩后的长度，这时再使用getContentLength()方法来取出解压后的数据就是错误的了。正确的做法应该是一直调用InputStream.read()方法来读取响应数据，一直到出现-1为止。

我们在Android 2.3版本中还增加了一些HTTPS方面的改进，现在HttpsURLConnection会使用SNI(Server Name Indication)的方式进行连接，使得多个HTTPS主机可以共享同一个IP地址。除此之外，还增加了一些压缩和会话的机制。如果连接失败，它会自动去尝试重新进行连接。这使得HttpsURLConnection可以在不破坏老版本兼容性的前提下，更加高效地连接最新的服务器。

在Android 4.0版本中，我们又添加了一些响应的缓存机制。当缓存被安装后(调用HttpResponseCache的install()方法)，所有的HTTP请求都会满足以下三种情况：
所有的缓存响应都由本地存储来提供。因为没有必要去发起任务的网络连接请求，所有的响应都可以立刻获取到。
视情况而定的缓存响应必须要有服务器来进行更新检查。比如说客户端发起了一条类似于 “如果/foo.png这张图片发生了改变，就将它发送给我” 这样的请求，服务器需要将更新后的数据进行返回，或者返回一个304 Not Modified状态。如果请求的内容没有发生，客户端就不会下载任何数据。
没有缓存的响应都是由服务器直接提供的。这部分响应会在稍后存储到响应缓存中。
由于这个功能是在4.0之后的版本才有的，通常我们就可以使用反射的方式来启动响应缓存功能。下面的示例代码展示了如何在Android 4.0及以后的版本中去启用响应缓存的功能，同时还不会影响到之前的版本：

```
private void enableHttpResponseCache() {  
    try {  
        long httpCacheSize = 10 * 1024 * 1024; // 10 MiB  
        File httpCacheDir = new File(getCacheDir(), "http");  
        Class.forName("android.net.http.HttpResponseCache")  
            .getMethod("install", File.class, long.class)  
            .invoke(null, httpCacheDir, httpCacheSize);  
    } catch (Exception httpResponseCacheNotAvailable) {  
    }  
}  
```

你也应该同时配置一下你的Web服务器，在HTTP响应上加入缓存的消息头。

https://www.cnblogs.com/zhousysu/p/5483896.html
