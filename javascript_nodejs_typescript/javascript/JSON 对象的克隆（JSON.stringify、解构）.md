JSON 对象的克隆（JSON.stringify、解构）

@[toc]


# 一、文章参考
1. [带有function的JSON对象的序列化与还原](https://www.cnblogs.com/sparkbj/p/8203343.html)


# 二、带有function的JSON序列化
[JSON.stringify() MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)

## JSON.stringify 介绍
**JSON.stringify(value[, replacer [, space]])**

1. value
将要序列化成 一个 JSON 字符串的值。

2. `replacer 可选`
如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中；如果该参数为 null 或者未提供，则对象所有的属性都会被序列化。

3. space 可选
指定缩进用的空白字符串，用于美化输出（pretty-print）；如果参数是个数字，它代表有多少的空格；上限为10。该值若小于1，则意味着没有空格；如果该参数为字符串（当字符串长度超过10个字母，取其前10个字母），该字符串将被作为空格；如果该参数没有提供（或者为 null），将没有空格。

```js
let option = {
  name: "huangbiao",
  getName: function () {
    console.log(this.name)
    return this.name
  }
}
JSON.stringify(option, function(key, val) {
  if (typeof val === 'function') {
    return val + '';
  }
  return val;
}, 2)
```


# 三、将JSON字符串反序列化（包含function的字符串）
[JSON.parse MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)

## JSON.parse 介绍

** JSON.parse(text[, reviver]) **

1. text
要被解析成 JavaScript 值的字符串，关于JSON的语法格式,请参考：JSON。

2. `reviver 可选`
转换器, 如果传入该参数(函数)，可以用来修改解析生成的原始值，调用时机在 parse 函数返回之前。

```js
let userObj = {
  "name": "huangbiao",
  "getName": "function () {\n    console.log(this.name)\n    return this.name\n  }"
}

// 由于JSON带有function的字符串不太好表示，因此使用一个 JSON 对象代替
let userObjStr = JSON.stringify(userObj)

// 将字符串转为
let tempObj = JSON.parse(userObjStr,function(k,v){
  if(v.indexOf&&v.indexOf('function')>-1){
     return eval("(function(){return "+v+" })()")
  }
  return v;
})
```


# 四、综合整理 —— 克隆方法
```js
cloneObject: function (obj) {
  let objStr = JSON.stringify(obj, function(key, val) {
    if (typeof val === 'function') {
      return val + '';
    }
    return val;
  })
  return JSON.parse(objStr,function(k,v){
    if(v.indexOf&&v.indexOf('function')>-1){
       return eval("(function(){return "+v+" })()")
    }
    return v;
  });
}
```

# 五、JSON对象解构实现克隆 {...obj}

```js
const originObj = {
  name: 'huangbiao',
  getName: function () {
    console.log(this.name)
  }
}

originObj.getName()

// 直接序列化和反序列化 会过滤掉方法
const cloneOne = JSON.parse(JSON.stringify(originObj))
console.log(cloneOne) // { name: 'huangbiao' }

// 通过结构，克隆对象，不是深拷贝
const cloneTwo = { ...originObj }
console.log(cloneTwo) // { name: 'huangbiao', getName: [Function: getName] }
console.log(originObj === cloneTwo) // false
cloneTwo.name = 'huanghaili'
cloneTwo.getName() // huanghaili
```



