---
title: mouse Event
date: 2017-11-17 14:22:22
tags:
---
* mousedown

e.bubbles: 是否冒泡 true
e.target: 事件对应的 DOM 树顶级元素（最顶端的元素）
e.cancelable: 事件是否可以取消 true
e.clientX
e.clientY

* mouseup

e.bubbles: true
e.target
e.cancelable

----

* mouseover

鼠标移动到目标元素上触发 移出不触发 在目标元素上父子节点切换移动也会触发(因为冒泡)
e.bubbles: true
e.cancelbale: true

* mouseout

鼠标移出目标元素触发 在目标元素上父子节点切换移动也会触发(因为冒泡)
e.bubbles: true
e.cancelbale: true

* mousemove

鼠标只要在目标上（包括子元素）移动就持续触发

----

* mouseenter

鼠标只在移入目标元素时触发
e.bubbles: false
e.cancelbale: false

* mouseleave

鼠标只在移出目标元素时触发
e.bubbles: false
e.cancelbale: false

---

* scroll

在document视图或者一个element在滚动的时候，会触发元素的scroll事件。
e.bubbles: false
e.cancelable: false
```
.wrap overflow: scroll width: 100px height: 100px
   .box width: 1000px height: 1000px
(.wrap).addEventListener('scroll', fn)
```

* wheel

鼠标在目标元素上 滚轮滚动时触发 方向键或拖动滚动条不触发
e.bubbles: true
e.cancelbale: true
