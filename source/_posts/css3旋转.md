---
title: css3旋转
date: 2016-12-19 13:19:13
tags:
---
先记下以后再说
主要说一下transform-origin为什么是100%
```css
    .container{
      display: none;
    }
    .loading{
      background: #EDEFF1;
      height: 100%;
    }
    .loading img{
      position: absolute;
      right: 50%;
      bottom:50%;
      animation: change .8s linear infinite;
      -webkit-animation: change .8s linear infinite;
      transform-origin: 100% 100%;
      -webkit-transform-origin: 100% 100%;
    }

    @-webkit-keyframes change {
      from{
        transform: rotate(0deg) translate(50%,50%);
        -webkit-transform: rotate(0deg) translate(50%,50%);
      }
      to{
        transform: rotate(360deg) translate(50%,50%);
        -webkit-transform: rotate(360deg) translate(50%,50%);
      }
    }
```