---
title: temp
date: 2018-07-25 13:47:14
tags:
---
6中基本数据类型 string boolean number symbol null undefined
NaN !== NaN

301 永久重定向  表示之前的资源被永久性删除了 搜索引擎抓取新内容 将旧的网址重定向到新的网址
302 临时重定向  表示之前的资源还存在 搜索引擎抓取新内容 而保存之前的网址

route
  history
  浏览器前进后退  触发popstate事件
  点击跳转  调用pushState方法向浏览器历史添加一个状态 并不会向服务器请求
  刷新页面或输入URL  会向服务器请求 所以使用history方式需要后端配合重定向
  hash
  浏览器前进后退 点击跳转 会触发hashChange事件 并不会向服务器请求
  刷新页面或输入URL  会向服务器请求 触发load事件
expires/Cache-Control 浏览器本地缓存
If-modified-Since/LastModified If-None-match/ETag 304
* 性能
网络层面：
  DNS 预解析
  ```html
  <link rel="dns-prefetch" href="http://www.example.com">
  ```
  缓存
  减少http请求
  使用http2.0 可以多路复用 让多个请求使用同一个TCP连接
  预加载 (有些资源不需要马上用到，但希望尽早获取) 强制浏览器请求资源，并且不会阻塞onload事件 rel="preload"
  预渲染 预渲染将下载的文件预先在后台渲染 rel="prerender"
优化渲染过程
  懒执行
  懒加载
文件优化
  图片优化 用CSS代替 加载合适大小的图片 小图用base64 雪碧图 图片压缩
  其他文件优化 压缩 gzip cssjs的位置
  cdn 增加并发请求上限

渲染大量数据性能问题解决方案：
浏览器每秒渲染60次 也就是说每一帧大约是16ms 为了防止页面卡顿 要让js的操作量尽量控制在允许的范围之内
window.requestAnimationFrame()方法会在下一次重绘之前执行传入的回调，那就可以用递归的方式每一帧渲染10条数据

* 使用 Webpack 优化项目
1. 对于 Webpack4，打包项目使用 production 模式，这样会自动开启代码压缩
2. 使用 ES6 模块来开启 tree shaking，这个技术可以移除没有使用的代码
3. 优化图片，对于小图可以使用 base64 的方式写入文件中
4. 按照路由拆分代码，实现按需加载
5. 给打包出来的文件名添加哈希，实现浏览器缓存文件

webpack fis3
webpack必须从一个js的入口文件出发 将依赖的的文件进行提取 编译 打包
fis3将所有的文件进行编译 生成资源配置表 根据资源配置表去配置各种打包策略

vue简述
1. 首先 他会去执行一个compileToFunctions()方法 将 模板 解析成 render函数
2. 之后 render函数 可以编译成 Vnode 它在读取其中的变量的时候 会创建一个 Watcher 的实例 通过
Object.defineProperty 的 get 方法 将该 watcher 收集到一个对应的 Dep 实例上 开始监听
3. 之后 可以将Vnode通过 patch()方 法渲染成真实的DOM 并挂载到页面的节点上
4. 最后 当data属性变化的时候 会触发 Object.defineProterty() 的 set 方法 修改值 并且通知 watcher 去触发vNode的更新

loaders:
vue-loader
babel-loader
json-loader
file-loader
url-loader
css-loader style-loader

plugins: 
html-webpack-plugin: 根据html模板插入script, link标签
webpack.HotModuleReplacementPlugin: 用于模块热替换
webpack.optimize.UglifyJsPlugin: 混淆代码
extract-text-webpack-plugin: 从js模块中提取css到独立文件中
DefinePlugin: 设置webpack环境变量
CommonsChunkPlugin: 提取公共部分


promise中的nextTick降级方案：nono
setImmediatesetImmediate
process.nextTick
setTimeout



throw new Error('xx') // 抛出错误  之后的代码就不运行了

node api:
http
path
querystring
Buffer
stream
error
events
fs


Nuxt.js 主要关注的是应用的 UI渲染。
初始化新项目的基础结构代码，或者在已有 Node.js 项目中使用 Nuxt.js。
预设了利用Vue.js开发服务端渲染的应用所需要的各种配置
还提供生了成对应的静态站点的功能。
作为框架，Nuxt.js 为 客户端/服务端 这种典型的应用架构模式提供了许多有用的特性，例如异步数据加载、中间件支持、布局支持等。


yoman
npm i yo -g
npm i generator-generator -g

yo-generator

接收用户输入
根据用户输入生成模板文件
将模板文件拷贝到目标目录
安装依赖



前端异常监控
1. js异常处理
  try-catch 只能捕获 运行时非异步错误， 对语法错误和异步错误无能为力
  window.onerror 能捕获到所有运行时错误
  promise错误 只能用catch方法来捕获 全局捕获 window.addEventListener("unhandledrejection", function(e){})
2. 异常上报方式
  ajax发送数据
  动态创建img标签的形式



跨域
jsonp:
  首先前端需要先设置好回调函数，并将其作为 url 的参数。
  服务端接收到请求后，通过该参数获取到回调函数名，并将数据放在参数中将其返回
  收到结果后因为是 script 标签，所以浏览器会当做是脚本进行运行，从而达到跨域获取数据的目的

CORS 跨站资源共享
  服务端返回response的请求头
  Access-Control-Allow-Origin