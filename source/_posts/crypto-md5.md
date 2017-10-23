---
title: crypto-md5
date: 2017-10-23 13:58:04
tags:
---
MD5(Message-Digest Algorithm) 是广泛使用的散列函数（又称哈希算法，摘要算法），主要用来确保消息的完整和一致性。常见的应用场景有密码保护、下载文件校验等。

特点：
* 运算速度快。
* 输出长度固定（128位）。
* 运算不可逆。
* 高度离散：输入的微小变化，可导致输出结果的巨大差异。
* 弱碰撞性：不同输入的散列值可能相同。

<!-- more -->

node:
```js
const crypto = require('crypto')
const md5 = crypto.createHash('md5')

const result = md5.update('abc').digest('hex')

console.log(result) // 900150983cd24fb0d6963f7d28e17f72
```

(1) 密码直接加密：
容易暴力破解

(2) 密码加盐加密：
在密码特定位置加上特定的字符串，再MD5

(3) 密码随机加盐加密


MD5碰撞
