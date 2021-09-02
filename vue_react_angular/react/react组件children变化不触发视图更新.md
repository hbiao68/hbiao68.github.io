react组件children变化不触发视图更新
@[toc]

# 一、文章参考
1. [React.Children  API 介绍](https://zh-hans.reactjs.org/docs/react-api.html#reactchildren)

# 二、props.children

> 它包含组件的开始标签和结束标签之间的内容

## 2.1 快速入门

```js
<Welcome>Hello world!</Welcome>
```

## 2.2 使用 —— 函数组件
```js
function Welcome(props) {
  return <p>{props.children}</p>;
}
```

## 2.3 使用 —— class组件
```js
class Welcome extends React.Component {
  render() {
    return <p>{this.props.children}</p>;
  }
}
```

# 三、React.Children	
> React.Children 提供了用于处理 this.props.children 不透明数据结构的实用方法

## 3.1 API 介绍
1. React.Children.map

```js
React.Children.map(children, function[(thisArg)])
```

在 children 里的每个直接子节点上调用一个函数，并将 this 设置为 thisArg

注意:`如果 children 是一个 Fragment 对象，它将被视为单一子节点的情况处理，而不会被遍历`。

2. React.Children.forEach

与 React.Children.map() 类似，但它不会返回一个数组。

3. React.Children.count

返回 children 中的组件总数量，等同于通过 map 或 forEach 调用回调函数的次数。

4. React.Children.only

```js
React.Children.only(children)
```

验证 children 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。

注意: React.Children.only() 不接受 React.Children.map() 的返回值，因为它是一个数组而并不是 React 元素。

5. React.Children.toArray

```js
React.Children.toArray(children)
```
将 children 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key
例子

# 四、react组件children变化不触发视图更新

## 4.1 问题介绍
1. 创建一个布局组件  <CommonPanelWrap>
2. 将页面的的内容通过其他组件定义
3. 根据state中的数据变化，触发 <CommonPanelWrap>内部的children变化

```js
class LoginPage extends BaseComponent {
  constructor (props) {
    super(props)
    this.state = {
      panelType: 'login' // 面板的状态 login  scan  findpwd1  findpwd2
    }
  }

  // 改变面板的内容
  changePanel (type) {
    this.setState({
      panelType: type
    })
  }

  // 获取面板的内容
  getPanelHtml () {
    const { panelType } = this.state
    let result = null
    if (panelType === 'login') {
      result = <LoginFormComp {...this.props} changePanel={this.changePanel.bind(this)}></LoginFormComp>
    } else if (panelType === 'scan') {
      result = <ScanQRComp {...this.props} changePanel={this.changePanel.bind(this)}></ScanQRComp>
    } else if (panelType === 'findpwd1') {
      result = <FindPwdOne {...this.props}></FindPwdOne>
    } else if (panelType === 'findpwd2') {
      result = <FindPwdTwo {...this.props}></FindPwdTwo>
    }
    return result
  }

  render () {
    const { panelType } = this.state
    const panelHtml = this.getPanelHtml()
    return (
      <CommonPanelWrap>
        {panelHtml}
      </CommonPanelWrap>
    )
  }
}
```


## 4.2 问题分析
1. 将逻辑组件 <ScanQRComp> 直接放到 布局组件  <CommonPanelWrap>中可以显示

2. 断点调试，确信 组件已经重新渲染了，并且children 发生了变化

3. 测试 state变化，将 <CommonPanelWrap>和业务组件<ScanQRComp>绑定一起重新渲染，界面会发生变化
> 功能实现问题不大了：最差的办法就是状态变化，就把<CommonPanelWrap>布局组件和业务组件一起渲染，虽然布局组件没有任何变化

4. 推测 <CommonPanelWrap> 组件阻止了视图的变化，因为布局组件确实从来没有变化，导致react 的diff算法任务没有变化，就不会重新渲染
> 验证办法：将布局组件添加一个key，这个key就是 state.panelType 的值
		
## 4.3 渲染知识点

1. React的diff 算法不是针对整棵树，知识针对当前节点及节点一下的子节点
如果当前节点没有变化，就不会继续渲染当前节点下的子节点

2. diff 判断是否重复的依据就是key

## 4.4 解决办法

在布局中添加 key 用来标识

```js
render () {
  const { panelType } = this.state
  const panelHtml = this.getPanelHtml()
  return (
    <CommonPanelWrap key={panelType}>
      {panelHtml}
    </CommonPanelWrap>
  )
}
```

