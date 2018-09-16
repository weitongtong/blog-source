---
title: vue
date: 2017-09-18 15:41:30
tags:
---

#### 1. vue实例
  ``` javascript
  var vm = new Vue({
    el: '#app', // 挂载点 实例挂载之后，元素可以用 vm.$el 访问
    template: // 字符串模板，模板将会 替换 挂载的元素。如果选项中包含 render 函数，则 template 将被忽略
    data: {
      message: 'hello, world'
    },
  })
  ```
#### 2. vue.extend, vue.component
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

#### 3. 运行时+编译器 VS 只包含运行时
如果你需要在客户端编译模板（比如传入一个字符串给`template`选项，或挂载到一个元素上并以其内部的`HTML`作为模板），你将需要加上编译器，即完整版的构建。
```js
// 需要编译器
new Vue({
  template: '<div>{{hi}}</div>'
})

// 不需要编译器
new Vue({
  render(h) {
    return h('div', this.hi)
  }
})
```
当使用 `vue-loader`或`verify`的时候，`*.vue`文件内部的模板会在构建时预编译成js。你在最终打包好的包里是不需要编译器的，因为只是运行时构建即可。


#### 知识点
```HTML
<child :message="message" name="child" @click="clickHandle"></child>
```
* v-bind="$attrs" 继承属性
* v-on="$listeners" 继承事件


* [Element] v-loading visible使用了$nextTick

* [Element] tips:
  * console.warn('[Element Warn][Form]model is required for resetFields to work.')
  * throw new Error('must call validateField with valid prop string!');
<!-- more -->

生命周期：
<img style="margin: 0 auto;" src="http://oifogbmox.bkt.clouddn.com/170918_2.png">
