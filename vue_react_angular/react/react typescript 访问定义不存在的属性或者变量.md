@[toc]

# 一、文章参考
1. [react-slick Previous and Next methods](https://react-slick.neostack.com/docs/example/previous-next-methods)
2. [react官网——Refs and the DOM](https://react.docschina.org/docs/refs-and-the-dom.html)

# 二、问题一：如何让编译机能监听到 props和state定义的interface

## 2.1 问题说明

在自己写的React项目中，定义了一个基类 BaseComponent，自己定义的所有类都要继承该类，实现统一管理某些“公共方法”的目的，由于BaseComponent 是一个使用 typescript 编写的，而且编译器检测 props 和 state 的值，是需要将inteface 是放到 React.Component 中的，有与中间隔了一个 BaseComponent ，这样就导致编译器无法检测具体业务组件需要传递的Props 和 State 的interface了

## 2.2. 解决办法 —— 使用泛型传递特性

1. 定义好 "基础类"

```js
import React, { Component } from 'react'

export default class BaseComponent<P, S> extends Component<P, S> {
  constructor (props: P) {
    super(props)
  }

  // 获取用户对象
  getUserInfo (isGoLoginPage = true) {
    const props: any = this.props
    const userinfoStr = '{}'
    // 如果没有该字段，则返回null
    if (userinfoStr) {
      return JSON.parse(userinfoStr)
    } else {
      if (isGoLoginPage) {
        props.history.push('/login')
      }
      return null
    }
  }
}
```

2. 业务类继承 “基础类”


```js
import React from 'react'
// 直接引入scss文件，转为了css
import AsyncComponent from '@/core/AsyncComponent'
import BaseComponent from '@/core/BaseComponent'
import { Iprops } from '@/model'
const CommonPanelWrap = AsyncComponent(() => require('../child/CommonPanelWrap'))

interface Istate {
  panelType: string
}
class LoginPage extends BaseComponent<Iprops, Istate> {
  constructor (props: Iprops) {
    super(props)
    this.state = {
      panelType: 'login' // 面板的状态 login  scan  findpwd1  findpwd2
    }
  }

  render () {
    const { panelType } = this.state
    return (
      <CommonPanelWrap key={panelType}>
        aaaaa
      </CommonPanelWrap>
    )
  }
}

export default LoginPage
```

# 二、问题二：在“基类”中去调用ts没有定义的属性


```js
import React, { Component } from 'react'

export default class BaseComponent<P, S> extends Component<P, S> {
  constructor (props: P) {
    super(props)
  }

  // 获取用户对象
  getUserInfo (isGoLoginPage = true) {
    // 定义一个变量 props: any ，欺骗编译器
    const props: any = this.props
    const userinfoStr = '{}'
    // 如果没有该字段，则返回null
    if (userinfoStr) {
      return JSON.parse(userinfoStr)
    } else {
      if (isGoLoginPage) {
        // 后面就可以访问对应的属性和方法了
        props.history.push('/login')
      }
      return null
    }
  }
}
```

