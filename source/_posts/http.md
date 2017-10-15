---
title: http
date: 2017-10-15 21:47:20
tags:
---
#### HTTP
HTTP超文本传输协议，构建在TCP之上，属于应用层协议。

```bash
$ curl -v http://127.0.0.1:4001
```

<!-- more -->

http模块:
Node 的 http 模块包含对 HTTP 处理的封装。在 Node 中， HTTP 服务继承自 TCP 服务器（net 模块），它能够与多个客户端保持连接，由于其采用事件驱动的形式，并不为每一个连接创建额外的线程或进程，保持很低的内存占用，所以能实现高并发。HTTP 服务与 TCP 服务模型有区别的地方在于，在开启 keepalive 后，一个 TCP 会话可以用于多次请求和响应。TCP 服务以 connection 为单位进行服务，HTTP 服务以 request 为单位进行服务。http 模块即是将 connection 到 request 的过程进行了封装，如下：