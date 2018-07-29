---
title: android studio ndk error
date: 2018/6/1 9:26:41
tags:
categories: [Android, ndk, AS]
---

如果您尝试使用SDK Manager重新安装NDK，则无法解决该问题。从NDK下载页面下载 android-ndk-r16b-darwin-x86_64.zip文件并找到，toolchains/mips64el-linux-android-4.9/prebuilt/darwin-x86_64/，将它复制到NDK文件夹，错误消失。
