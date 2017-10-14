---
title: TCP & UDP
date: 2017-10-14 20:56:22
tags:
---
Node 事件驱动、无阻塞、单线程
利用Node搭建网络服务器，不需要额外的容器（Apache,tomcat等）
Node 提供了 net、dgram、http、https 4个模块，分别用于处理 TCP、UDP、HTTP、HTTPS ，适用于服务端和客户端。

<!-- more -->

#### 构建 TCP 服务
#### TCP
TCP 全名为传输控制协议，在 OSI 模型中属于传输层协议。许多应用层协议基于 TCP 构建，典型的是 HTTP SMTP IMAP 协议。
<img style="width: 350px" src="http://oifogbmox.bkt.clouddn.com/171014-1-OSI.png" />
TCP 是面向连接的协议，其显著特征是在传输之前需要3次握手形成会话：
<img style="width: 350px" src="http://oifogbmox.bkt.clouddn.com/171014-2-tcp.png" />
只有会话形成之后，服务器和客户端之间才能互相发送数据。在创建会话的过程中，服务器端和客户端分别提供一个套接字，这两个套接字共同形成一个连接。服务器端与客户端则通过套接字实现两者之间连接的操作。

#### 创建TCP服务器端

```js
var net = require('net')
var server = net.createServer(function(socket) {
  // 新的连接
  socket.on('data', function(data) {
    socket.write('你好')
  })
  socket.on('end', function() {
    console.log('连接断开')
  })
  socket.write('欢迎你。。。')
})
server.listen(4003, function() {
  console.log('server bound')
})
```
我们通过 net.createServer(listener) 即可创建一个 TCP 服务器，`listener`是连接事件`connection`的侦听器，也可以采用如下的方式：
```js
var server = net.createServer()
server.on('connection', function(socket) {
  // 新的连接
})
server.listen(4003)
```
我们可以利用 Telnet 工具作为客户端对刚才创建的简单服务器进行会话交流，如下：
![images](http://oifogbmox.bkt.clouddn.com/171014-3.png)

> telnet 退出：commond + ] quit

通过 net 模块自行构造客户端进行会话，测试上面构建的 TCP 服务，如下：
client.js
```js
var net = require('net')
var client = net.connect({ port: 4003 }, function() { // 'connect' listener
  console.log('client connected')
  client.write('world!\r\n')
})
client.on('data', function(data) {
  console.log(data.toString())
  client.end() // 结束
})
client.on('end', function() {
  console.log('client disconnected')
}) 
```
Domain Socket ??

#### TCP 服务的事件

(1) 服务器事件
对于通过 net.createServer() 创建的服务器而言，它是一个 EventEmitter 实例，它的自定义事件有以下几种：
* listening
* connection: 每个客户端套接字连接到服务器端时触发。
* close
* error

(2) 连接事件
服务器可以同时与多个客户端保持连接。
* data
* end
* connect
* drain: 当任意一端调用 write()发送数据时，当前这端会触发该事件。
* error
* close
* timeout: 当一定时间后连接不再活跃时，该事件将会被触发，通知用户当前该连接已经被闲置了。

另外，由于 TCP 套接字是可读的 Stream 对象，可以利用 pipe() 方法巧妙地实现管道操作，如下代码实现一个 echo 服务器：
```js
var net = require('net')
var server = net.createServer(function(socket) {
  socket.write('Echo server\r\n')
  scoket.pipe(socket)
})
server.listen(4003, '127.0.0.1')
```
值得注意的是， 在Node中默认开启 Nagle 算法，只有当缓冲区的数据达到一定数量或者一定时间后才将其发出，以此来优化网络。这种优化虽然使网络带宽被有效地使用，但是数据有可能被延迟发送。

### 构建UDP服务

<div class="to-be-continued"></div>