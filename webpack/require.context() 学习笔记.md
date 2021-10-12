require.context()

# 一、参考
1. [webpack  require.context](https://webpack.js.org/guides/dependency-management/#requirecontext)
2. [加快Vue项目的开发速度](https://juejin.cn/post/6844903735668244488#heading-1)


# 二、问题描述
如果需要这样引入文件
```js
import A from 'components/A'
import B from 'components/B'
import C from 'components/C'
import D from 'components/D'
// ...
```
每一个引入就很麻烦，但是有规律——在同一个文件夹下面，那么，是否可以通过代码批量引入处理？

# 三、require.context是什么
一个webpack的api,通过执行require.context函数获取一个特定的上下文,`主要用来实现自动化导入模块,在前端工程中,如果遇到从一个文件夹引入很多模块的情况,可以使用这个api,它会遍历文件夹中的指定文件,然后自动导入,使得不需要每次显式的调用import导入模块`

## 3.1 语法
```js
require.context(
  directory,
  (useSubdirectories = true),
  (regExp = /^\.\/.*$/),
  (mode = 'sync')
);
```
参数
* directory {String} -读取文件的路径
* useSubdirectories {Boolean} -是否遍历文件的子目录
* regExp {RegExp} -匹配文件的正则
* mode  是否同步获取，默认是同步

返回值
* `require.context函数执行后返回的是一个函数`
  * resolve {Function} -接受一个参数request,request为test文件夹下面匹配文件的相对路径,返回这个匹配文件相对于整个工程的相对路径
  * keys {Function} -返回匹配成功模块的名字组成的数组
  * id {String} -执行环境的id,返回的是一个字符串,主要用在module.hot.accept,应该是热加载?
  * require.context函数执行后返回的是一个函数,也接受一个req参数,这个和resolve方法的req参数是一样的,即匹配的文件名的相对路径,而files函数返回的是一个模块

## 语法案例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/256264f67cc141faa3b803b12700495e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

```js
// modulesFiles是一个具有 resolve, keys, id 三个属性的方法
// 首先调用require.context导入某个文件夹的所有匹配文件,返回执行上下文的环境赋值给modulesFiles变量
const modulesFiles = require.context('./modules', true, /\.js$/)
// modulesFiles 是一个方法
console.log(modulesFiles)
// modulesFiles 函数的keys方法返回modules文件夹下所有以.js结尾的文件的文件名,返回文件名组成的数组
console.log('keys', modulesFiles.keys())
// id 是上下文模块里面所包含的模块 id. 它可能在你使用 module.hot.accept 的时候被用到
// 获取文件的目录 以及匹配规则
console.log('id', modulesFiles.id)
// 根据相对路径， 获取到文件的全路径
console.log('resolve', modulesFiles.resolve(modulesFiles.keys()[0]))
// 获取指定路径下的模块内容
// 因为我的路径是用export default导出的,所以在Module模块的default属性中获取到我导出的内容
console.log('content', modulesFiles(modulesFiles.keys()[0]))
```

**打印结果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f07886d10fd46739668d2ba93f97be5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


# 四、使用场景
## 案例一：Vue 批量引入路由

```js
const files = require.context('.', true, /\.js$/)

console.log(files.keys()) // ["./home.js"] 返回一个数组
let configRouters = []
/**
* inject routers
*/
files.keys().forEach(key => {
  if (key === './index.js') return
  configRouters = configRouters.concat(files(key).default) // 读取出文件中的default模块
})
export default configRouters // 抛出一个Vue-router期待的结构的数组
```

## 案例二： 定义全局组件

```js
import Vue from 'vue'
let contexts = require.context('.', false, /\.vue$/)
contexts.keys().forEach(component => {
  let componentEntity = contexts(component).default
  // 使用内置的组件名称 进行全局组件注册
  Vue.component(componentEntity.name, componentEntity)
})

```

## 案例三： Vuex

```js
const modulesFiles = require.context('./modules', true, /\.js$/)
// console.log(modulesFiles)
// debugger

// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})

const store = new Vuex.Store({
  modules,
  getters
})

export default store
```