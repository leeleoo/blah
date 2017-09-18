---
title: 想用history搞事吗
date: 2016-11-01 10:48:24
tags:
---

# 速记

## 两个函数

> 以下两个函数唯一不同点replaceState不会在 history 中加入新的记录

1. history.pushState
```javascript
history.pushState({},title,url)
```
2.  history.replaceState
```javascript
history.replaceState({},title,url)
```
*以上方法不会触发onhashchange*
<!-- more -->
## 几个事件
### window.onhashchange
	
> 如何触发?

1. 直接更改浏览器地址，在最后面增加或改变#hash；
2. 通过改变location.href或location.hash的值；
3. 通过触发点击带锚点的链接；
4. 浏览器前进后退可能导致hash的变化，前提是两个网页地址中的hash值不同。

```javascript
window.onhashchange = function(event) {
    console.log(event.oldURL);
    console.log(event.newURL);
    console.log(location.hash);
};
```


### window.onpopstate
*使用这个事件有以下几点要注意*

1. 这个事件是唯一可以访问到之前改变 State 的函数中第一个参数的方法
2. 这个事件触发的时机
3. 事件作用范围仅在于一个document里面
4. `history.pushState`会增加历史记录的条目，但是不会触发`hashchange`和`popstate`;`hashchange`也可以增加历史记录的条目，但是它却可以触发popstate。

* history.pushState和history.replaceState都不会触发这个事件

* 仅在浏览器前进后退操作、history.go/back/forward调用、hashchange的时候触发




参考:[流云诸葛](www.cnblogs.com/lyzg/p/5960609.html)

