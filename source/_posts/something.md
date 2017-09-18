---
title: something
date: 2017-09-18 10:35:55
tags:
---
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