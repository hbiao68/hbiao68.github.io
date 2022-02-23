



# 一、参考

1. [一篇文章学会 Vue 项目单元测试](https://zhuanlan.zhihu.com/p/48758013)
2. [Vue Test Utils 官网](https://vue-test-utils.vuejs.org/zh/)
   		
   	
   		
# 二、环境搭建

## 2.1 安装依赖
```bash
cnpm install vue jest vue-jest babel-jest @vue/test-utils @babel/core @babel/preset-env  vue-template-compiler -D

// @vue/test-utils 依赖的是 babel-core
cnpm install babel-core@^7.0.0-bridge.0 --save-dev
   			
cnpm install --save-dev jsdom jsdom-global
```

## 2.2 配置 babel.config.js
```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          node: "current",
        },
      },
    ],
  ],
};
```

## 2.3 配置 jest.config.js
```js
/*
 * For a detailed explanation regarding each configuration property, visit:
 * https://jestjs.io/docs/configuration
 */
const path = require("path")

module.exports = {
  
  moduleFileExtensions: [ // 类似 webpack.resolve.extensions
    "vue",
    "js",
    "jsx",
    "ts",
    "tsx",
    "json",
    "node"
  ],

  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1', // 类似 webpack.resolve.alias
  },
  rootDir: path.resolve(__dirname, "./"),

  // A list of paths to directories that Jest should use to search for files in
  // roots: [
  //   "<rootDir>"
  // ],
  
  // 配置Vue 的全局配置，即所有的测试用例都会先执行setup.js 文件
  setupFiles: ["<rootDir>/test/setup.js"], 

  testEnvironment: "jsdom",

  testPathIgnorePatterns: [
    "\\\\node_modules\\\\"
  ],
  transform: {
    // 类似 webpack.module.rules
    "^.+\\.js$": "<rootDir>/node_modules/babel-jest",
    ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest",
  },
  transformIgnorePatterns: [
    "\\\\node_modules\\\\",
    "\\.pnp\\.[^\\\\]+$"
  ],
};
```
