---
title: 实现一个CMD模块加载器
date: 2017-09-20 11:02:58
tags:
---

[资料](http://blog.csdn.net/zhoujie_zhoujie/article/details/70173430)


## 模块加载流程

  ![image](http://oifogbmox.bkt.clouddn.com/170920-cmd.png)

  1. 首先，通过 use 方法来加载入口模块，并接收一个回调函数， 当模块加载完成， 会调用回调函数，并传入对应的模块。use 方法会 check 模块有没有缓存，如果有，则从缓存中获取模块，如果没有，则创建并加载模块。
  2. 获取到模块后，模块可能还没有 load 完成，所以需要在模块上绑定一个 “complete” 事件，模块加载完成会触发这个事件，这时候才调用回调函数。
  3. 创建一个模块时，id就是模块的地址，通过创建 script 标签的方式异步加载模块的代码（factory），factory 加载完成后，会 check factory 中有没有 require 别的子模块:
    - 如果有，继续加载其子模块，并在子模块上绑定 “complete” 事件，来触发本身 的 “complete” 事件；
    - 如果没有则直接触发本身的 “complete” 事件。
  4. 如果子模块中还有依赖，则会递归这个过程。
  5. 通过事件由里到外的传递，当所有依赖的模块都 complete 的时候，最外层的入口模块才会触发 “complete” 事件，use 方法中的回调函数才会被调用。