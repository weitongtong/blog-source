---
title: 关于vue
date: 2017-09-18 15:41:30
tags:
---

生命周期：
<img style="margin: 0 auto;" src="http://oifogbmox.bkt.clouddn.com/170918_2.png">

1. vue实例
  ``` javascript
  var vm = new Vue({
    el: '#app', // 挂载点 实例挂载之后，元素可以用 vm.$el 访问
    template: // 字符串模板，模板将会 替换 挂载的元素。如果选项中包含 render 函数，则 template 将被忽略
    data: {
      message: 'hello, world'
    },
  })
  ```
2. vue.extend, vue.component
  ``` javascript
  var myComponentOption = {
    template: '<div>{{message}}</div>',
    data() {
      return {
      }
    },
    props: ['message']
  }
  var Bvue = Vue.extend(bComponentOption)
  var bVue = new Bvue({
    el: '#bb', // way-1
    propsData: {
      message: 'hello vue',
    }
  })
  // way-2
  // bVue.$mount('#bb')

  // way-3
  // bVue.$mount()
  // document.getElementById('bb').appendChild(bVue.$el)

  // 注册全局组件
  var bComponent = Vue.component('b-component', Bvue)

  bVue instanceof Vue // true
  bComponent instanceof Vue // false
  ```
