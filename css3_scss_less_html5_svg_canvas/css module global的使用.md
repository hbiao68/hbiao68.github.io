@[toc]

# 一、文章参考
1. (https://www.cnblogs.com/kugeliu/p/7889018.html)[https://www.cnblogs.com/kugeliu/p/7889018.html]

# 二、问题描述
在React 项目中，使用的是css module 来管理样式，现在需要写样式覆盖到组件内部组件默认样式，因为css module会将样式本身定义的名字改变，所以要设置全局变量，让CSS module不做转换。

# 三、global语法说明

## 单个全局class
```css
// css 样式
(:global)(.test1) {
    color: blue;
}
```

## 多个全局class
```css
:global {
    .test1 {
        color: blue;
    }
    .test2 {
        color: red;
    }
}
```

# webpack 如何配置css module
```xml
{
    test: /\.css$/,
    use: [
        {
            loader: 'style-loader'
        },
        {
            loader: 'css-loader?modules&localIdentName=[name]-[hash:base64:5]'
        }
    ]
},
```
> 针对css-loader，后面要跟着modules

# 四、在CSS module中定义全局变量

```css
// 用户标签
.tagItems {
  display: inline-block;
  margin-left: 0.12rem;
  margin-bottom: 0.16rem;
  position: relative;
  &--close{
    position: absolute;
    top: -0.08rem;
    right: -0.08rem;
    cursor: pointer;
    display: none;
  }
  &:hover{
    .tagItems--close{
      display: inline-block;
      background: #018e4a;
      border-radius: 8px;
      width: 16px;
      height: 16px;
      text-align: center;
      color: #fff;
      line-height: 16px;
    }
  }
  // 定义全局样式，不会被 CSS moudle 转换
  :global(.ant-btn-dangerous) {
    color: #018e4a;
    background: #fff;
    border-color: #018e4a;
  }
}
```
