---
title: 移动端网络优化方向
date: 2017-08-17 09:35:46
categories:
- 优化
tags:
- 网络
---

﻿一个网络请求可以简单分为连接服务器 -> 获取数据两个部分。

##  一. 连接服务器优化

* 不用域名，用 IP 直连（gslb）
省去首次域名解析一般需要几百毫秒， 预防域名劫持带来的风险
* 服务器部署
动态 IP选择最优的服务器IP进行连接， 服务器端还可以调优服务器的 TCP 拥塞窗口大小、重传超时时间(RTO)、最大传输单元(MTU)

## 二. 获取数据优化

* 连接复用(三次握手方面)
节省连接建立时间，如开启 keep-alive
* 合并请求
* 减小请求数据大小
post请求的body用gzip压缩， 请求头压缩， 
* CDN缓存
* 减小返回数据大小
api数据gzip压缩， 数据格式json, protobuf, 多个图片分变率， webp格式， 增量更新， 断点续传

## 三. 其它优化

* 预加载
* 延迟加载
* 并发请求