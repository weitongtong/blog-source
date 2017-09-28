---
title: axios
date: 2017-09-27 18:05:04
tags:
---
axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端。
具备以下特征：
* 从浏览器中创建 XMLHttpRequest
* 从 node.js 发出 http 请求
* 支持 Promise API
* 拦截请求和响应
* 转换请求和响应数据
* 取消请求
* 自动转换JSON数据
* 客户端支持防止 CSRF/XSRF
---
直接看几个例子吧
``` js
// get

axios.get('/api/aa?id=12345').then((res) => {
  // do something...
}).catch((err) => {
  // do something...
})

axios.get('/api/aa', {
  params: {
    id: 12345,
  }
}).then((res) => {
  // do something...
}).catch((err) => {
  // do something...
})

// post
axios.post('/usr', {
  name: 'bb',
  age: 10
}).then((res) => {
  // do something...
}).catch((err) => {
  // do something...
})

// 多个请求并发
function getDataA() {
  return axios.get('/api/aa')
}
function getDataB() {
  return axios.get('/api/bb')
}
axios.all([getDataA(), getDataB()]).then((res) => {
  // do something...
}).catch((err) => {
  // do something...
})
```

返回数据：

``` js
axios.get('url').then((res) => {
  res.data // 数据
  res.status // 200
  res.statusText // OK
  res.headers // response头
  res.config // 配置
})
```

拦截器：

``` js
// 全局添加请求拦截器
axios.interceptors.request.use((config) => {}
  // 在发送请求之前 do something
  return config
},(err) => {
  // 请求错误时 do something
  return Promise.reject(err)
})
 
// 全局添加响应拦截器
axios.interceptors.response.use((res) => {
  // 对响应数据 do something
  return res
}, (error) => {
  //请求错误时 do something
  return Promise.reject(error)
})

// 删除拦截器
const myInterceptor = axios.interceptors.request.use(() => {
  // do something
})
axios.interceptors.request.eject(myInterceptor)

// 自定义 axios 实例 添加拦截器
const instance = axios.create()
instance.interceptors.request.use((config) => {
  // do something
})
```