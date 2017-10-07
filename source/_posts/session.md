---
title: session
date: 2017-10-02 00:10:03
tags:
---
cookie 存在的问题：
* 体积
* 前后端都可以修改

session 数据只保留在服务器端，客户端无法修改，如何将每个客户和服务器中数据一一对应起来，有常见的两种实现方式：

<!-- more -->

#### 1. 基于 cookie 来实现用户和数据的映射
服务器端约定一个键值 作为 session 的口令，这个值可以随意约定。
一旦服务器检查到用户请求 cookie 中没有携带该值，它就会为之生成一个值，这个值是唯一且不重复的值，并设定超时时间。
```js
var sessions = {}
var key = 'session_id'
var EXPIRES = 20 * 60 * 1000

var generate = function() {
  var session = {}
  session.id = (new Date()).getTime() + Math.random()
  session.cookie = {
    expire: (new Date()).getTime() + EXPIRES
  }
  sessions[session.id] = session
  return session
}
```
每个请求到来时，检查 cookie 中的口令与服务器端的数据，如果过期，就重新生成。
```js
function (req, res) {
  var id = req.cookies[key]
  if (!id) {
    req.session = generate()
  } else {
    var session = sessions[id]
    if (session) {
      if (session.cookie.expire > (new Date()).getTime()) {
        // 更新超时时间
        session.cookie.expire = (new Date()).getTime() + EXPIRES
        req.session = session
      } else {
        // 超时了，删除旧的数据，并重新生成
        delete sessions[id]
        req.session = generate()
      }
    }
  }
  handle(req, res)
}
```
当然仅仅重新生成 session 还不足以完成整个流程，还需要在响应给客户端时设置新的值，以便下次请求时能够对应服务器端的数据。这里我们 hack 响应对象 writeHead() 方法，在它内部注入设置 cookie 的逻辑，如下：
```js
var writeHead = res.writeHead
res.writeHead = function() {
  var cookies = res.getHeader('Set-Cookie')
  var session = serialize(key, req.session.id)
  // Set-Cookie 字段 可能存在多条，所以 cookies 可能是个数组。
  cookies = Array.isArray(cookies) ? cookies.concat(session) : [cookies, session]
  res.setHeader('Set-Cookie', cookies)
  return writeHead.apply(this, arguments)
}
```
至此， session 在前后端进行对应的过程就完成了。这样的业务逻辑可以判断和设置session，以此来维护用户与服务器端的关系，如下：
```js
var handle = function(req, res) {
  if (!req.session.isVisit) {
    req.session.isVisit = true
    res.writeHead(200)
    res.end('第一次访问')
  } else {
    res.writeHead(200)
    res.end('再次访问')
  }
}
```
这样在 session 中保存的数据比直接在 cookie 中保存数据要安全的多。这种方式依赖 cookie 实现，而且也是目前大多数 Web 应用的方案。

#### 2. 通过查询字符串来实现浏览器端和服务器端数据的对应

未完待续。。。