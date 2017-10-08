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

#### (1) 基于 cookie 来实现用户和数据的映射
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

#### (2) 通过查询字符串来实现浏览器端和服务器端数据的对应
它的原理是检查请求的查询字符串，如果没有值，会先生成新的带值的 url
```js
var getURL = function(_url, key, value) {
  var obj = url.parse(_url, true)
  obj.query[key] = value
  return url.format(obj)
}
```
然后形成跳转，让客户端重新发起请求
```js
function(req, res) {
  var redirect = function(url) {
    res.setHeader('Location', url)
    res.writeHead(302)
    res.end()
  }

  var id = req.query[key]
  if (!id) {
    var session = generate()
    redirect(getURL(req.url, key, session.id))
  } else {
    var session = sessions[id]
    if (session) {
      if (session.cookie.expire > (new Date()).getTime()) {
        // 更新超时时间
        session.cookie.expire = (new Date()).getTime() + EXPIRES
        req.session = session
        handle(req, res)
      } else {
        // 超时了，删除旧的数据，并重新生成
        delete sessions[id]
        var session = generate()
        redirect(getURL(req.url, key, session.id))
      }
    } 
  }
}
```
用户访问`http://localhost/pathname/`时，如果服务器端发现查询字符串中不带`session_id`参数，就会将用户跳转到 `http://localhost/pathname?session_id=12323`这样一个类似的地址。如果浏览器收到`302`状态码和`Location`报头，就会重新发起新的请求，如下：
```
< HTTP/1.1 302 Moved Temporarily
< Location: /pathname?session_id=12323
```
这样，新的请求到来时就能通过`Session`的检查，除非内存中的数据过期。

当然 这种方式比 通过`Cookie`还不安全。


#### Session与内存
可能的问题：
(1) 我们的`sessions`是存在内存中的，而`Node`中存在内存限制，用户一旦增多，数据量会过大，引起垃圾回收的频繁扫描，产生性能问题。
(2) 我们为了利用多核`CPU`而启动了多个进程，用户请求的连接将随意分配到各个进程中，`Node`的进程与进程之间是不能直接共享内存的，用户的`Session`可能会引起错乱。
通常的解决方案：
将`Session`集中化，将原本可能分散在多个进程里的数据，统一转移到集中的数据存储中。目前常用的工具是`Redis`、`Memcached`等，通过这些高效的缓存，`Node`进程无须在内部维护数据对象，垃圾回收问题和内存限制等问题都可以迎刃而解了。

采用第三方缓存来存储`Session`引起的一个问题是会引起网络访问。理论上来说访问网络中的数据要比访问磁盘中的数据速度要慢，因为涉及到握手、传输以及网络终端自身的磁盘I/O等，尽管如此但依然会采用这些高速缓存的理由有以下：
* Node 与缓存服务保持长连接，而非频繁的短连接，握手导致的延迟只影响初始化。
* 高速缓存直接在内存中进行数据存储和访问。
* 缓存服务通常与 Node 进程运行在相同的机器上或者相同的机房里，网络速度受到的影响较小。

这样，Session 就需要通过异步的方式来获取了：
```js
function(req, res) {
  var id = req.cookies[key]
  if (!id) {
    req.session = generate()
    handle(req, res)
  } else {
    store.get(id, function(err, session) {
      if (session) {
        if (session.cookie.expire > (new Date()).getTime()) {
          // 更新超时时间
          session.cookie.expire = (new Date()).getTime() + EXPIRES
          req.session = session
        } else {
          // 超时了，删除旧数据，重新生成session
          delete sessions[id]
          req.session = generate()
        }
      } else {
        // 如果session过期或口令不对，重新生成session
        req.session = generate()
      }
      handle(req, res)
    })
  }
}
```
在响应时，将新的`session`保存回缓存中：
```js
var writeHead = res.writeHead
res.writeHead = function() {
  var cookies = res.getHeader('Set-Cookie') // 暂存之前设置的cookie
  var session = serialize(key, req.session.id)
  cookies = Array.isArray(cookies) ? cookies.concat(session) : [cookies, session]
  res.setHeader('Set-Cookie', cookies)

  // 保存回缓存
  store.save(req.session)

  return writeHead.apply(this, arguments)
}
```

#### session与安全

```js
// 将值通过私钥签名，由.分割原值和签名
var sign = function(val, secret) {
  return val + '.' + crypto
    .createHmac('sha256', secret)
    .update(val)
    .digest('base64')
    .replace(/\=+$/, '')
}
```
在响应时，设置`session`值到`cookie`中：
```js
var val = sign(req.sessionID, secret) // secret 为定义了一个私钥
res.setHeader('Set-Cookie', cookie.serialize(key, val))
```
接收请求时，检查签名：
```js
// 取出口令部分进行签名，对比用户提交的值
var unsign = function(val, secret) {
  var str = val.slice(0, val.lastIndexOf('.'))
  return sign(val, secret) === val ? str : false
}
```
这样一来，即使攻击者知道口令中`.`号前的值是服务端`session`的`ID`值，只要不知道`secret`私钥的值，就无法伪造签名信息，以此实现对`session`的保护。
