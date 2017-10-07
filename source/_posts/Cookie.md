---
title: Cookie
date: 2017-09-29 01:18:07
tags:
---
http是无状态的协议。如何标识和认证一个用户，cookie应运而生。

Cookie 是由浏览器和服务器共同协作实现的规范，步骤如下：
* 服务器向客户端发送 Cookie 。
* 浏览器将 Cookie 保存。
* 之后每次浏览器都会将 Cookie 发向服务器端。

HTTP_Parser 会将所有的报文字段解析到 req.headers 上，那么 Cookie 就是 req.headers.cookie 。
根据规范的定义， Cookie 值的格式是 `key=value; key2=value` 形式的。解析如下：
```js
var parseCookie = function (cookie) {
  var cookies = {}
  if (!cookie) {
    return cookies
  }
  var list = cookie.split(';')
  for (var i = 0; i < list.length; i++) {
    var pair = list[i].split('=')
    cookies[pair[0].trim()] = pair[1]
  }
  return cookies
}
```
在业务逻辑代码之前，我们将其挂载在 req 对象上，让业务代码可以直接访问，如下：
``` js
function(req, res) {
  req.cookies = parseCookie(req.headers.cookie)
  handle(req, res)
}
```

---

服务端设置 Cookie

响应的 Cookie 值在 Set-Cookie 字段中。它的格式与请求中的格式不太一样，规范为
`Set-Cookie: name=value; Path=/; Expires=Sun, 23-Apr-23 09:01:35 GMT; Domain=.domain.com;`
其中 name=value 是必须包含的部分，其余皆可选。
* path 表示这个 Cookie 影响到的路径，当前访问的路径不满足该匹配时，浏览器则不发送这个Cookie。
* Expires 和 Max-Age 是用来告知浏览器这个 Cookie 何时过期，如果不设置该选项，在关闭浏览器时会丢掉这个    
  Cookie 。如果设置了过期时间，浏览器将会把 Cookie 内容写入到磁盘中并保存，下次打开浏览器依旧有效。 Expires 的值是一个 UTC 格式的时间字符串，告知浏览器此 Cookie 何时将过期， Max-Age 则告知浏览器此 Cookie 多久后过期。前者一般而言不存在问题，但是如果服务器的时间和客户端的时间不能匹配，这种时间设置就会存在偏差。为此，Max-Age 告知浏览器这条 Cookie 多久之后过期，而不是一个具体的时间点。
* HttpOnly 告知浏览器不允许通过脚本 document.cookie 去更改这个 Cookie 值，事实上，设置了 HttpOnly 之后，这个值在 document.cookie 中不可见。但是在 HTTP 请求的过程中， 依然会发送这个 Cookie 到服务器端。
* Secure 当 Secure 值为 true 时，在 HTTP 中是无效的，在 HTTPS 中才有效，表示创建的 Cookie 只能在 HTTPS 连接中被浏览器传递到服务器端进行会话验证。

将 Cookie 序列化成符合规范的字符串：
```js
const serialize = function(name, val, opt = {}) {
  const pairs = [`${name}=${encode(val)}`]

  if (opt.maxAge) pairs.push('Max-Age=' + opt.maxAge)
  if (opt.domain) pairs.push('Domain=' + opt.domain)
  if (opt.path) pairs.push('Path=' + opt.path)
  if (opt.expires) pairs.push('Expires=' + opt.expires.toUTCString())
  // toUTCString格式： "Sat, 07 Oct 2017 14:22:16 GMT"

  if (opt.httpOnly) pairs.push('HttpOnly')
  if (opt.secure) pairs.push('Secure')

  return pairs.join('; ')
}

var handle = function(req, res) {
  if (!req.cookies.isVisit) {
    // 设置 Set-Cookie
    res.setHeader('Set-Cookie', serialize('isVisit', '1'))
    res.writeHead(200)
    res.end('第一次访问')
  } else {
    res.writeHead(200)
    res.end('再次访问')
  }
}
```

客户端收到这个带 Set-Cookie 的响应后，在之后的请求时会在 Cookie 字段中带上这个值。

值得注意的是， Set-Cookie 在报头中是可能存在多个字段的。为此 res.setHeader 的第二个参数可以是一个数组，如下：
```js
res.setHeader('Set-Cookie', [serialize('foo', 'bar'), serialize('baz', 'val')])
// 这会在报文头部中形成两条 Set-Cookie 字段：
// Set-Cookie: foo=bar; Path=/; Expires=Sun, 23-apr-23 09:01:35 GMT; Domain=.domain.com;
// Set-Cookie: baz=val; Path=/; Expires=Sun, 23-apr-23 09:01:35 GMT; Domain=.domain.com;
```

Cookie 的性能影响
* 减少 Cookie 大小
* 为静态资源使用不同的域名：减少无效 Cookie 的传输（静态资源一般不需要cookie中的标识信息）


