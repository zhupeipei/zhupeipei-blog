# JsBridge框架选型


####JS调用Android有下面三种方式

	•	webView.addJavascriptInterface()
	•	WebViewClient.shouldOverrideUrlLoading()
	•	WebChromeClient.onJsAlert()/onJsConfirm()/onJsPrompt() 方法分别回调拦截JS对话框alert()、confirm()、prompt()消息

####Android调用JS

	•	webView.loadUrl(); 缺点：页面会刷新
	•	webView.evaluateJavascript() 缺点：Android4.4以下不能使用

####JsBridge实现：

	a.	Android调用JS是通过loadUrl(url),url中可以拼接要传给JS的对象
	b.	JS调用Android是通过shouldOverrideUrlLoading/onJsPrompt
	c.	JsBridge将沟通数据封装成Message，然后放进Queue,再将Queue进行传输


####目前github上主流的JsBridge框架有：

| 框架(数据截至 2018-05-07) | star数 | 发布者背景 | 最近一次更新时间 | 拦截方式 |优点 | 缺点 |
|----------|:-------------:|------:|
| lzyzsd/JsBridge | 4.7K | 阿里巴巴 | 5month | shouldOverrideUrlLoading|js通过反射自动注入，无需引用| 只有Android版本，据说bug很多 |
| pedant/safe-java-js-webview-bridge | 2.2k |  | 3years | 通过onJsPrompt()实现| ||
| pengwei1024/JsBridge | 470 |  | 6month |shouldOverrideUrlLoading| 有IOS和Android版本||
|wendux/DSBridge-Android|615|google flutter|24days|shouldOverrideUrlLoading|lzyzsd的挑战版本，IOS JSBridge 10.9K||
| Lede-Inc/LDJSBridge_Android | 47 | 网易电商 | 3years | |有IOS和Android版本||
| Sunzxyong/JsBridge | 173 |  | 10month||||
| siva17/JSBridge | 56 |  | 1year ||||
|xesam/JsBridge | 65 |  | 4month | | |js需要引用|

备注：这篇文档并未尝试跑所有框架的demo，内容是根据github上各个框架的文档和issue以及其他一些资料获取到的。

JsBridge目前已经没有以前那么热了，目前更多的专注点在Weex和RN以及Google的Flutter和Fushia。

实际跑了下pengwei1024和wendux的JsBridge，对比如下：
1. pengwei1024是根据module来划分功能块，结构很清晰；wendux根据JsApi定义，结构分解也很不错；
2. pengwei1024实现功能比较实用，如获取定位信息、拍照（拍照完成后需要把照片在html中显示因为需要传输数据所以app会卡住）、分享等功能；
3. wendux有两个功能是pengwei1024没有的，一是可以实现一次调用多次返回（在上传进度条显示会很有效），二是与Fly.js结合无需webview设置跨域也能进行跨域请求（其原理是将请求拦截交给JsBridge处理）。
4. 两者均为url拦截，相对来说比onJsPrompt感觉更加合理些；
5. wendux有Android和IOS端，使用方式几乎无差别，便于开发；
6. wendux支持腾讯x5内核（微信），可以在微信中使用；
7. 两者均为个人维护项目，但是wendux的IOS端维护者有56人，文档较详细，感觉是有组织在推进的，另外最近更新时间也较新，说明项目还在持续更新中
