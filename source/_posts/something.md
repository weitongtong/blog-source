---
title: something
date: 2017-09-18 10:35:55
tags:
---
<style>
  .boxbox{
    border: 2px dashed #ddd;
    padding: 0 20px;
    margin-bottom: 20px;
  }
</style>

<div class="boxbox">
DOM文档加载的步骤为：
1. 解析HTML结构。
2. 加载外部脚本和样式表文件。
3. 解析并执行脚本代码。
4. DOM树构建完成。// DOMContentLoaded
5. 加载图片等外部文件。
6. 页面加载完毕。// load

```javascript
document.addEventListener('DOMContentLoaded', function() {
  // something...
}, false)
window.addEventListener("load", function() {
  // something...
}, false);
```
</div>

<div class="boxbox">
innerHTML: 从对象的起始位置到终止位置的全部内容,不包括Html标签。
outerHTML: 除了包含innerHTML的全部内容外, 还包含对象标签本身。
demo: 
``` javascript
<div id="test"> 
   <span style="color:red">test1</span> test2 
</div>
// innerHTML: "<span style="color:red">test1</span> test2 "
// outerHTML: "<div id="test"><span style="color:red">test1</span> test2 </div>"
```
</div>

<div class="boxbox">
判断是否为{}空对象
``` js
function isEmptyObj(obj) {
  if (obj === undefined || obj === null) {
    return false
  }
  return Object.getOwnPropertyNames(obj).length === 0
}
```
</div>

<div class="boxbox">
  [学习资料](http://caibaojian.com/book/)
</div>
