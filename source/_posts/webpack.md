---
title: webpack
date: 2018-05-04 10:35:20
tags:
---
 PostCss
 postcss 是一个css 处理工具，和scss不同的地方在于它通过插件机制可以灵活的扩展其支持的特性
 包括给css自动添加前缀，使用下一代css语法等

 PostCss和css的关系就像Babel和Javascript的关系，他们解除了语法上的禁锢，通过插件机制来扩展语言本身,
 用工程化手段给语言带来更多的可能性。
 PostCss和scss的关系像Babel和TypeScript的关系，PostCss更加灵活，可扩张性强，而SCSS内置了大量功能而不能扩展

 postcss.config.js
 ```js
 module.exports = {
   plugin: [
     // 需要使用的插件列表
     require('postcss-cssnext')
   ]
 }
 ```