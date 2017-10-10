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
  br{
    padding: 0;
    margin: 0;
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
Array.prototype.sort(compareFunction)

1. 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前。
2. 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
3. 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。

```js
const arr = [11, 41, -9046, 2047, 1118, 8477, 8446, 279, 4925, 7380, -1719, 3855]
arr.sort((a, b) => {
  return a - b // 切记 不要 直接返回一个Bolean类型的值 如： a < b 
})
```
</div>

<div class="boxbox">
node 调试命令
"debug": "node --debug-brk --inspect index.js"
</div>

<div class="boxbox">
301 Moved Permanently 永久性重定向，响应报文的Location首部应该有该资源的新URL。
302 Found 临时性重定向，响应报文的Location首部给出的URL用来临时定位资源。
304 Not Modified 服务器内容没有更新，可以直接读取浏览器缓存。
401 Unauthonzed 表示请求未经授权，该状态代码必须与 WWW-Authenticate 报头域一起使用。
403 Forbidden 表示服务器收到请求，但是拒绝提供服务，通常会在响应正文中给出不提供服务的原因。
404 Not Found 请求的资源不存在，例如，输入了错误的URL。
500 Internel Server Error 表示服务器发生不可预期的错误，导致无法完成客户端的请求。
503 Service Unavailable 表示服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常。

301和302的区别：

301和302状态码都表示重定向，就是说浏览器在拿到服务器返回的这个状态码后会自动跳转到一个新的URL地址，这个地址可以从响应的Location首部中获取（用户看到的效果就是他输入的地址A瞬间变成了另一个地址B）——这是它们的共同点。

他们的不同在于。301表示旧地址A的资源已经被永久地移除了（这个资源不可访问了），搜索引擎在抓取新内容的同时也将旧的网址交换为重定向之后的网址。

302表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。 SEO302好于301。
</div>

<div class="boxbox">
（1） 检测某个 属性 是否支持
核心思路：在任一元素的 element.style 对象上坚持该属性是否存在

```js
  function testProperty(property) {
    const root = document.documentElement // 整个<html></html>
    if (property in root) {
      root.classList.add(property.toLowerCase()) // toLowerCase转小写
      return true
    }
    root.classList.add(`no-${property.toLowerCase()}`)
    return false
  }
```

（2） 检测某个具体的 属性值 是否支持
核心思路：把它赋给对应的属性 再检查浏览器是不是还保存着这个值

```js
  function testValue(id, value, property) {
    const dummy = document.createElement('p')
    dummy.style[property] = value
    if (dummy.style[property]) {
      root.classList.add(id)
      return true
    }
    root.classList.add(`no-${id}`)
    return false
  }
```

（3） 当然 浏览器可以 解析 某个css特征 并不代表它已经实现 （或正确实现）了这个特性。
</div>

<div class="boxbox">

```
~version: 大概匹配某个版本
未指定的可以任意
如：~1.1.2，表示>=1.1.2 <1.2.0，可以是1.1.2，1.1.3，1.1.4，.....，1.1.n 
如：~1.1，表示>=1.1.0 <1.2.0，可以是同上
如：~1，表示>=1.0.0 <2.0.0，可以是1.0.0，1.0.1，1.0.2，.....，1.0.n，1.1.n，1.2.n，.....，1.n.n

^version: 兼容某个版本
版本号中最左边的非0数字的右侧可以任意
如果缺少某个版本号，则这个版本号的位置可以任意
如：^1.1.2 ，表示>=1.1.2 <2.0.0，可以是1.1.2，1.1.3，.....，1.1.n，1.2.n，.....，1.n.n
如：^0.2.3 ，表示>=0.2.3 <0.3.0，可以是0.2.3，0.2.4，.....，0.2.n
如：^0.0，表示 >=0.0.0 <0.1.0，可以是0.0.0，0.0.1，.....，0.0.n
```

</div>

<div class="boxbox">
  [可能有用的学习资料](http://caibaojian.com/book/)
</div>



<script>
  document.addEventListener('DOMContentLoaded', function() {
    document.querySelectorAll('.boxbox').forEach((dom) => {
      dom.removeChild(dom.firstChild)
      dom.removeChild(dom.lastChild)
    })
  }, false)
</script>