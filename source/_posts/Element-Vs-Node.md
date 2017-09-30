---
title: Element Vs Node
date: 2017-09-18 09:40:33
tags:
---
Node是一个接口，许多DOM类型从这个接口继承，并允许类似地处理（或测试）这些各种类型。

以下接口都从Node继承其方法和属性:
Document, Element, CharacterData (which Text, Comment, and CDATASection inherit), ProcessingInstruction, DocumentFragment, DocumentType, Notation, Entity, EntityReference

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
``` js
document.body // Element
document.getElementById('id') // Element
document.body.childNodes // 返回所有 node 子节点
document.body.children // 只返回 Element 子节点

document.body.firstChild // Node
document.body.firstElementChild // Element
```
![image](http://oifogbmox.bkt.clouddn.com/170918_1.png)


节点类型 | NodeType
---|---
元素节点 ELEMENT_NODE | 1
属性节点 ATTRIBUTE_NODE | 2
文本节点 TEXT_NODE | 3
注释节点 COMMENT_NODE | 8
文档 DOCUMENT_NODE | 9


Node 常用属性:
  - Node.firstChild
  - Node.lastChild
  - Node.nodeName // 节点名称
  - Node.nodeType // 节点的类型 Node.ELEMENT_NODE
  - Node.nodeValue // 对于文档节点来说, nodeValue返回null. 对于text, comment, 和 CDATA 节点来说, nodeValue返回该节点的文本内容. 对于 attribute 节点来说, 返回该属性的属性值.
  - ...




[MDN web docs]https://developer.mozilla.org/zh-CN/docs/Web/API/Node