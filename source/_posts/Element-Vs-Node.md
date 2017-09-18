---
title: Element Vs Node
date: 2017-09-18 09:40:33
tags:
---
直接看个dmeo:
```html
<html>
  <body>
    <h1>China</h1>
    <!-- My comment ...  -->
    <p>China is a popular country with...</p>
    <div>
      <button>See details</button>
    </div>
  </body>
</html>
```
![image](http://oifogbmox.bkt.clouddn.com/170918_1.png)

> Node是一个接口，许多DOM类型从这个接口继承， 如： Document, Element, CharacterData(Text, Comment, ..)...

Node 常用属性:
  - Node.firstChild
  - Node.lastChild
  - Node.nodeName // 节点名称
  - Node.nodeType // 节点的类型 Node.ELEMENT_NODE
  - Node.nodeValue // 对于文档节点来说, nodeValue返回null. 对于text, comment, 和 CDATA 节点来说, nodeValue返回该节点的文本内容. 对于 attribute 节点来说, 返回该属性的属性值.
  - ...




[MDN web docs]https://developer.mozilla.org/zh-CN/docs/Web/API/Node