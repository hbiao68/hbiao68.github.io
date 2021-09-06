create-react-app默认配置进行自定义craco

@[toc]

# 一、参考
1. [github craco 安装](https://github.com/gsoft-inc/craco/blob/master/packages/craco/README.md#installation)
2. [antUI在create-react-app使用](https://ant.design/docs/react/use-with-create-react-app-cn)


# 二、问题描述
项目使用 create-react-app 创建项目，UI 使用Ant-UI，现在需要修改antUI的主题，尝试了各种办法，效果不好，最后替换使用 craco 来启动项目


# 3 craco 介绍

## 3.1 craco 快速入门
1. 安装依赖
> yarn add @craco/craco
或者
> npm install @craco/craco --save

2. 修改package.json的script
```js
"scripts": {
-   "start": "react-scripts start",
-   "build": "react-scripts build",
-   "test": "react-scripts test",
+   "start": "craco start",
+   "build": "craco build",
+   "test": "craco test",
}
```

3. 启动 —— `npm start`

4. 发布 —— `npm run build`

5. 添加自定义的配置文件——在根目录下创建 craco.config.js
```js
module.exports = {
// ...
};
```

## 3.2 修改antUI主题

1. 把 src/App.css 文件修改为 src/App.less，然后修改样式引用为 less 文件
```js
src/App.js
- import './App.css';
+ import './App.less';
```

```js
src/App.less
- @import '~antd/dist/antd.css';
+ @import '~antd/dist/antd.less';
```

2. 安装 craco-less 
> yarn add craco-less

3. 并修改 craco.config.js
```js
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
};

```

## 3.3 添加别名
```js
const path = require('path')
const pathResolve = (pathUrl) => path.join(__dirname, pathUrl)

module.exports = {
  webpack: {
    // 设置别名
    alias: {
      '@': pathResolve('src'),
      '@assets': pathResolve('src/asserts'),
      '@images': pathResolve('src/asserts/images'),
      '@components': pathResolve('src/components'),
      '@hooks': pathResolve('src/hooks'),
      '@pages': pathResolve('src/pages'),
      '@utils': pathResolve('src/utils')
    }
  }
}

```

## 3.4 设置工程的路径

```js
module.exports = {
  webpack: {
    // 返回webpack的配置信息
    configure: (webpackConfig, { env, paths }) => {
      console.log(webpackConfig)
      // 设置项目的上下文目录
      // webpackConfig.output.publicPath = './hb'
      webpackConfig.output.publicPath = './'
      return webpackConfig
    }
  }
}
```

## 3.5 添加代理

```js
module.exports = {
  devServer: {
    port: 3000,
    proxy: {
      '/interview': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  }
}
```