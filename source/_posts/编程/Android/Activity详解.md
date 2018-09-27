---
title: Activity详解
date: 2018/7/31 17:26:41
tags:
categories: [Android, Activity]
---

### ActivityThread
ActivityThread是应用APP启动的Main Class，代码如下：
![ActivityThread 的Main方法](Activity详解/ActivityThread Main.png)
启动过程会经历如下的过程：
1. Looper.prepareMainLooper(); // 初始化Looper;
2. ActivityThread thread = new ActivityThread(); // ActivityThread对象
3. thread.attach(false); // attach 重点
    3.1 final IActivityManager mgr = ActivityManagerNative.getDefault(); // ActivityManager.getService();最新版的现在是这么写的
    3.2 mgr.attachApplication(mAppThread); // mAppThread用于Android系统和ActivityThread通信的对象
4. Looper.loop(); // Looper进入轮询

### ApplicationThread (即上面的mAppThread)
ApplicationThread是在ActivityThread中初始化的，用于和ActivityManagerService进行通信。

### 生命周期
Instrumention负责打开Activity的各种生命周期方法，创建Application对象，启动Activity

