---
title: vue-SSR
date: 2017-09-25 22:27:07
tags:
---
[官方文档](https://ssr.vuejs.org/zh/)

## Why SSR
* SEO
* 更快内容到达(time-to-content)

## 基本用法
simple demo:
``` bash
$ npm i vue vue-server-renderer koa -S
```

<!-- more -->

server.js
```js
// server.js

const Vue = require('vue')
const Koa = require('koa')
const { createRenderer } = require('vue-server-renderer')
const app = new Koa()
const createApp = require('./app')

const indexRenderer = createRenderer({
  template: require('fs').readFileSync('./index.template.html', 'utf-8')
})

app.use( async (ctx) => {
  const context = {
    url: ctx.req.url,
    title: 'hello',
    meta: `<meta charset="utf-8">`
  }
  const app = createApp(context) // 获取 Vue 实例

  // 把 vue 实例 转成 html 并注入到 renderer 的模板中
  indexRenderer.renderToString(app, context, (err, html) => {
    if (err) {
      ctx.res.status = 500
      ctx.res.body = '500了'
      return
    }
    ctx.body = html
  })
})

app.listen(3000)
console.log("open http://localhost:3000")
```

app.js
```js
// app.js

const Vue = require('vue')

module.exports = function createApp(context) {
  return new Vue({
    data: {
      url: context.url
    },
    template: `<div>
                <div>我是 vue 实例中的内容</div>
                <div>访问的 url 是： {{ url }}</div>
              </div>`
  })
}
```

index.template.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- 使用双花括号(double-mustache)进行 HTML 转义插值(HTML-escaped interpolation) -->
  <title>{{ title }}</title>
  <!-- 使用三花括号(triple-mustache)进行 HTML 不转义插值(non-HTML-escaped interpolation) -->
  {{{ meta }}}
</head>
<body>
  <div>我是 renderer 的内容</div>
  <!-- 以下为template插入的注释标识 （注意 前后没空格） -->
  <!--vue-ssr-outlet-->
</body>
</html>
```
![images](http://oifogbmox.bkt.clouddn.com/170930-1.png)


![images](http://oifogbmox.bkt.clouddn.com/171002-3ssr-webpack.png)

未完待续。。。

