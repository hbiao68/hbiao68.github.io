# 一、文章参考

1. [分享8个非常实用的Vue自定义指令](https://juejin.cn/post/6906028995133833230#heading-0)
2. [Vue3 自定义指令 官方文档](https://v3.cn.vuejs.org/guide/custom-directive.html#%E7%AE%80%E4%BB%8B)



# 二、问题描述



工作中有时候会遇到需要实现点击复制的功能，但是不了解实现的原理，只是在网上查找到相关代码，然后copy 了事，无意间发现了有人将复制使用vue指令实现，并解释了如何实现的原理，在此做记录学习



# 三、案例实现



## 需求：

实现一键复制文本内容，用于鼠标右键粘贴。



## 思路：

1. 动态创建 `textarea` 标签，并设置 `readOnly` 属性及移出可视区域
2. 将要复制的值赋给 `textarea` 标签的 `value` 属性，并插入到 `body`
3. 选中值 `textarea` 并复制
4. 将 `body` 中插入的 `textarea` 移除
5. 在第一次调用时绑定事件，在解绑时移除事件



## Vue3 中定义指令



### Vue3 指令钩子函数



一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

- `created`：在绑定元素的 attribute 或事件监听器被应用之前调用。在指令需要附加须要在普通的 `v-on` 事件监听器前调用的事件监听器时，这很有用。
- `beforeMount`：当指令第一次绑定到元素并且在挂载父组件之前调用。
- `mounted`：在绑定元素的父组件被挂载后调用。
- `beforeUpdate`：在更新包含组件的 VNode 之前调用。

提示

我们会在[稍后](https://v3.cn.vuejs.org/guide/render-function.html#虚拟-dom-树)讨论渲染函数时介绍更多 VNodes 的细节。

- `updated`：在包含组件的 VNode **及其子组件的 VNode** 更新后调用。
- `beforeUnmount`：在卸载绑定元素的父组件之前调用
- `unmounted`：当指令与元素解除绑定且父组件已卸载时，只调用一次。

### v-copy指令实现



```js
export const copy = {
  mounted: function (el, { value }) {
    console.log(arguments)
    el.$value = value
    
    // el控件定义 onclick 事件
    el.onclick = () => {
      // if (!el.$value) {
      //   // 值为空的时候，给出提示。可根据项目UI仔细设计
      //   console.log('无复制内容')
      //   return
      // }
      // 动态创建 textarea 标签
      const textarea = document.createElement('textarea')
      // 将该 textarea 设为 readonly 防止 iOS 下自动唤起键盘，同时将 textarea 移出可视区域
      textarea.readOnly = 'readonly'
      textarea.style.position = 'absolute'
      textarea.style.left = '-9999px'
      // 将要 copy 的值赋给 textarea 标签的 value 属性
      // textarea.value = el.$value
      textarea.value ='huangbia11111111o'
      // 将 textarea 插入到 body 中
      document.body.appendChild(textarea)
      // 选中值并复制
      textarea.select()
      const result = document.execCommand('Copy')
      if (result) {
        console.log('复制成功') // 可根据项目UI仔细设计
      }
      document.body.removeChild(textarea)
    }
    // 绑定点击事件，就是所谓的一键 copy 啦
    el.addEventListener('click', el.handler)
  },
  // 当传进来的值更新的时候触发
  beforeUpdate(el, { value }) {
    el.$value = value
  },
  // 指令与元素解绑的时候，移除事件绑定
  unmounted(el) {
    el.removeEventListener('click', el.handler)
  },
}

export default copy

```



## Vue3 定义全局指令


1. 创建index.js 文件用来引入指令
```js
import copy from './copyDirective'

export default function globalDirectivRegist (rootApp) {
  rootApp.directive('copy', copy)
}
```

2. 利用root Vue对象定义全局指令
```js
import globalDirectivRegist from './directive/index'

const app = createApp(App)
globalDirectivRegist(app)
```



## 使用

```HTML
<span v-copy>huangbiao1111</span>
```



