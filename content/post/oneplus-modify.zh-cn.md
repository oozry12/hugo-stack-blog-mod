---
title: 一加 Ace 2 Pro 折腾记录
date: 2023-11-15T19:14:49+08:00
lastmod: 2024-02-04T11:34:49+08:00
tags: 
- phone
- oneplus
---

> 从一加 8 Pro 换到一加 Ace 2 Pro ，Android 还是折腾一下才好玩

## 系统
一定在出厂版本要备份 *全部分区* ，也就是俗称的字库~~~~
还有不要用`Fastboot Enhance` 这个图形化软件，不清楚分区情况。一刷就寄了。血的教训啊，不然天天跑售后！


2024.02.06 去常平售后刷回C13.411版本 

[x] 遗憾现在很少类原生rom

### 回到出厂包
用这个 `PJA110domestic_11_13.1.1.302CN01_2023072223340105.zip` 用流出来的那个9008软件(windows平台)，回归到最原始包（真正的出厂状态）

### 升级，然后解锁BL
OTA升级到 `color 13.1 552` ，重启到 fastboot 模式，`flash oem unlock` 解锁BL

### root，再安装模块
一键root，再安装`magisk hide`，绕过银行类app

## 照片
经常备份到 NAS（Network Attached Storage 网络附加存储），妈妈再也不用担心刷机

## git
- 配置好，手机发博客
- 学习Liunx系统