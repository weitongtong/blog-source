---
title: syncRequest
date: 2017-09-21 13:13:47
tags:
---
``` javascript
/**
  * 同步请求
  * @param {any} obj 请求对象
  * @returns {promise} http
  */
function syncRequest ({ method = 'GET', url, data, headers = {} }) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.open(method, url, false)
    xhr.setRequestHeader('Content-Type', 'application/json;charset=utf-8')

    Object.keys(headers).forEach(header => {
      xhr.setRequestHeader(header, headers[header])
    })

    xhr.onload = () => {
      if (xhr.readyState !== 4) {
        return
      }
      let responseContent
      try {
        responseContent = JSON.parse(xhr.responseText)
      } catch (err) {
        // there is error
      }
      responseContent = responseContent || xhr.responseText

      if (xhr.status === 200) {
        resolve(responseContent)
      } else {
        reject(responseContent)
      }
    }

    xhr.send(JSON.stringify(data))
  })
}

syncRequest({
  method: 'put',
  url: 'xxx',
  data: {},
  headers: {
    Accept: 'application/json, text/plain, */*; charset=utf-8',
  },
}).then((res) => {
  // something...
}).catch((err) => {
  // something...
})
```