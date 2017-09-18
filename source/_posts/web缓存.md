---
title: web缓存
date: 2016-12-24 11:20:21
tags:
---
三个关键字 `cache-control` ,`Expire`, `ETag` 

<!-- more -->
1. Expire与cache-control有一样的效果.他们都会设置一个过期时间,在这个时间内浏览器会从本地(disk, momery)直接读取而不发出http请求

2.  ETag 与 他们的区别在于:
当缓存到期时,  浏览器会发出一个http请求并带上Etag与服务器相对应的资源做比较,相同则返回304 继续读取本地缓存,不同则拉取新的资源200
 
*通常在线上的情况,根据业务需求设置一个合理的缓存时间对用户的体验有极大的提高,因为连请求都不用发送了*

然鹅,对于缓存的控制还有很多细节[参考资料](http://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html)