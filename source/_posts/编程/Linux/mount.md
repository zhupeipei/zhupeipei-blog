---
title: mount命令
date: 2018/6/1 9:26:41
tags:
categories: [Linux, mount]
---

----->>>>来自小白分享
测试环境的共享目录配置
服务器端

服务器

 10.203.27.173

目录

 /midware/share/fileservice

修改配置

 /etc/export

权限配置

 /midware/share/fileservice *(rw)

客户端

下载服务器（10.203.27.174）

  mount -t nfs -o rw 10.203.27.173:/midware/share/fileservice /midware/public

上传服务器（10.203.26.101/102）

  mount -t nfs -o rw 10.203.27.173:/midware/share/fileservice /midware/fileservice/upload
