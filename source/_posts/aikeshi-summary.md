---
title: aikeshi-summary
date: 2018-03-19 23:46:46
tags:
---
* axios
  ```js
  defaultHeaders = {
    Accept: 'application/json, text/plain, */*; charset=utf-8',
    'Content-Type': 'application/json; charset=unf-8',
    Pragma: 'no-cache',
    'Cache-Control': 'no-cache',
  }
  Object.assign(axios.defaults.headers.common, defaultHeaders)
  const methods = ['get', 'post', 'put', 'delete']
  const http = {}
  // 统一外部的调用 方式
  methods.forEach(method => {
    http[method] = axios[method].bind(axios)
  })
  export default http
  export const addRequestInterceptor =
  axios.interceptors.request.use.bind(axios.interceptors.request)

  export const addResponseInterceptor =
  axios.interceptors.response.use.bind(axios.interceptors.response)


  install(Vue) {
    Vue.prototype.http = http
  }
  ```