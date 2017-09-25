---
title: ' 在用 react 和 flex 实现瀑布流时踩的坑'
date: 2017-09-19 11:49:43
tags:
---
一 .flex box 的坑

当父元素设置为 `display:flex`

1.子元素没有设置高度
使用 offsetHeight 取到的是宽度


2.子元素设置高度
使用 offsetHeight 取到的是高度

3.子元素如果不设置为`flex-start` 使用 offsetHeight 拿到的将会是容器的高度



二. PureComponent的坑

1.PureComponent  每次 比较的是引用类型的地址,  所以你每次都得返回一个新对象