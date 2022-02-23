vue.config.js中配置proxy代理https

@[tac]

# 一、参考
1. [https://www.cnblogs.com/roland-sky/p/12916645.html](https://www.cnblogs.com/roland-sky/p/12916645.html)

# vue.config.js 配置

```js
devServer: {
  // 如果改动node_modules内的代码, 不会触发热重载, 则取消下面的注释
  // watchOptions: {
  //   ignored: []
  // },
  proxy: {
    '^/api/': {
      target: 'http://localhost:8060',
      changeOrigin: true
    },
    "/apis": {
      target: 'https://example.com',
      changeOrigin: true, 
      secure: false,
      headers: {                  
        Referer: 'https://example.com'
      }
    },
  }
}
```

配置说明：
* secure 安全证书校验
* Referer 表示请求的来源（必填）







































