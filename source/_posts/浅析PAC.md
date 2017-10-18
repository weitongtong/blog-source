---
title: 浅析PAC
date: 2017-10-17 14:55:27
tags:
---
Shadowsocks是目前很流行的翻墙工具，轻量等。
它提供两种模式：
* 全局模式（不解释，你懂的）
* PAC代理模式

<!-- more -->

#### 什么是PAC
自动代理配置(Proxy auto-config) 是一种网页浏览器技术，用于定义浏览器该如何自动选择适当的代理服务器来访问一个网址。
一个`PAC`文件包含一个`Javascript`形式的函数`FindProxyForURL(url, host)`。这个函数返回一个包含一个或多个访问规则的字符串。用户代理根据这些规则适用一个特定代理或直接访问。当一个代理服务器无法响应时，多个访问规则提供了其他的后备访问方法。
浏览器在访问页面以前，首先访问这个`PAC`文件。`PAC`文件中的`URL`可能是手工配置的，也可能是通过网页的网络代理自发协议自动配置的。
![images](http://oifogbmox.bkt.clouddn.com/171017-1.png)
简单的讲，`PAC`就是一种配置，它能让你的浏览器智能判断哪些网站走代理，哪些不走代理。
![images](http://oifogbmox.bkt.clouddn.com/171017-2.png)
![images](http://oifogbmox.bkt.clouddn.com/171017-3.png)

#### PAC的优势
PAC自动代理属于智能判断模式，相比全局代理，它的优点有：
* 不影响国内网站的访问速度，防止无意义的绕路
* 节省Shadowsocks服务的流量，节省服务器资源
* 控制方便

#### 配置文件语法
方式一：gfwlist.js文件 rule中添加。
方式二：user-rule.txt文件中添加。

自定义代理规则的设置语法与GFWlist相同，如下：
* 通配符支持。比如`*.example.com/*`实际书写时可省略`*`， 如`.example.com/`，和`*.example.com/*`效果一样。
* 正则表达式支持。以`\`开始和结束，如`\[\w]+:\/\/example.com\`。
* 例外规则`@@`，如`@@*.example.com/*`满足`@@`后规则的地址不使用代理。
* 匹配地址开始和结尾`|`，如`|http://example.com`、`example.com|`分别表示以`http://example.com`开始和以`example.com`结束的地址。
* `||`标记，如`||example.com`则`http://example.com`、`https://example.com`、`ftp://example.com`等地址均满足条件。
* 注释`!`， 如`!我是注释`。

> 注意：如果改 user-rule.txt 文件，需要 '从GFWList更新PAC'