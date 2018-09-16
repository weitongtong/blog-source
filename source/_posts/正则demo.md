---
title: 正则demo
date: 2018-04-02 11:18:09
tags:
---
```js
var str = '中国不好<span style="">中国好</span>中国<span>中国good</span>中国垃圾'
str.replace(/<span.*?<\/span>|中国/g, a => a.startsWith('<span') ? a : '美国')
// 美国不好<span style="">中国好</span>美国<span>中国good</span>美国垃圾
// .*? 非贪婪模式
```
```js
// 去掉前后空格
var str = '   fdfdf   '
str.replace(/^\s*|\s*$/, '') 
```