



# 文章参考

1. [js-base64 npm](https://www.npmjs.com/package/js-base64)



# 问题描述

工作中，java 开发同事需要将返回的 JSON 对象返回给 “第三方控件”，然后通过第三方控件 返回给web 浏览器，结果出现了乱码，经过定位反复调试，都没有解决该问题，由于“第三方控件”对于开发来说是黑盒，**于是最终的解决办法是 java将返回的JSON对象转为Base64字符串，web浏览器拿到Base64字符串之后，再反解析为标准的JSON 对象**。



# js-base64 库实现base64转换



## 安装引用依赖库

```bash
npm install --save js-base64
```



```html
<script src="https://cdn.jsdelivr.net/npm/js-base64@3.7.2/base64.min.js"></script>
```



## 使用库



```js
import { Base64 } from 'js-base64';
import { encode, decode } from 'js-base64';
```



```html
<script type="module">
// note jsdelivr.net does not automatically minify .mjs
import { Base64 } from 'https://cdn.jsdelivr.net/npm/js-base64@3.7.2/base64.mjs';
</script>

<script type="module">
// or if you prefer no Base64 namespace
import { encode, decode } from 'https://cdn.jsdelivr.net/npm/js-base64@3.7.2/base64.mjs';
</script>
```



# 使用demo



## base64 针对解析JSON 对象

```js
const tempObj = {
  name: 'huangbiao',
  age: 33,
  address: '长沙'
}
const tempObjStr = `{
  name: 'huangbiao',
  age: 33,
  address: '长沙'
}`
const base64Str = encode(tempObj)
console.log(base64Str) // W29iamVjdCBPYmplY3Rd
const objStr = decode(base64Str)
console.log(objStr) // [object Object]
```



## base64 解析JSON对象字符串

```js
const tempObjStr = `{
  name: 'huangbiao',
  age: 33,
  address: '长沙'
}`
const base64Str = encode(tempObjStr)
console.log(base64Str) // ewogIG5hbWU6ICdodWFuZ2JpYW8nLAogIGFnZTogMzMsCiAgYWRkcmVzczogJ+mVv+aymScKfQ==
const objStr = decode(base64Str)
console.log(objStr)
// {
//   name: 'huangbiao',
//   age: 33,
//   address: '长沙'
// }
```



## 结论

1. base64 只能用来解析 字符串，无法解析JSON对象
2. 如果要解析JSON对象，可以将JSON对象序列化转为JSON对象字符串