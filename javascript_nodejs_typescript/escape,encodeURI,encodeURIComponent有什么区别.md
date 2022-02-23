escape,encodeURI,encodeURIComponent有什么区别?

@[toc]

# 一、参考
1. [escape,encodeURI,encodeURIComponent有什么区别?](https://www.zhihu.com/question/21861899)
2. [encodeURIComponent()  MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)

# 二、函数介绍

## 2.1 escape(string)

* 概念
escape是对字符串(string)进行编码，这样就可以在所有的计算机上读取该字符串。 
* 返回值
已编码的 string 的副本。其中某些字符被替换成了十六进制的转义序列。
* 说明
  * 该方法不会对 ASCII 字母和数字进行编码，
  * 也不会对下面这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。
  * 其他所有的字符都会被转义序列替换。 (ASCII码的部分值也会被替换)
* 逆函数
unescape

```js
const v1 = escape('http://www.w3school.com.cn/')
console.log(v1) // http%3A//www.w3school.com.cn/
const v2 = escape('http://www.w3school.com.cn/My first/')
console.log(v2) // http%3A//www.w3school.com.cn/My%20first/
const v3 = escape(',/?:@&=+$#')
console.log(v3) // %2C/%3F%3A@%26%3D+%24%23
console.log(unescape('%2C/%3F%3A@%26%3D+%24%23')) // ,/?:@&=+$#
```

## 2.2 encodeURI(URIstring)
* 定义和用法
encodeURI() 函数可把字符串作为 URI 进行编码。
* 返回值
URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。
* 说明
  * 该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,# 
  * 该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

* 逆函数
decodeURI

```js
const v1 = encodeURI('http://www.w3school.com.cn/')
console.log(v1) // http://www.w3school.com.cn/
const v2 = encodeURI('http://www.w3school.com.cn/My first/')
console.log(v2) // http://www.w3school.com.cn/My%20first/
const v3 = encodeURI(',/?:@&=+$#')
console.log(v3) // ,/?:@&=+$#
```

## 2.3 encodeURIComponent(URIstring)
* 定义和用法
encodeURIComponent() 函数可把字符串作为 URI 组件进行编码。
* 返回值
URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。
* 说明
  * 该方法不会对 ASCII 字母和数字进行编码，
  * 也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。
  * 其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号），都是由一个或多个十六进制的转义序列替换的。

* 逆函数
decodeURIComponent 

```js
const v1 = encodeURIComponent('http://www.w3school.com.cn/')
console.log(v1) // http%3A%2F%2Fwww.w3school.com.cn%2F
const v2 = encodeURIComponent('http://www.w3school.com.cn/My first/')
console.log(v2) // http%3A%2F%2Fwww.w3school.com.cn%2FMy%20first%2F
const v3 = encodeURIComponent(',/?:@&=+$#')
console.log(v3) // %2C%2F%3F%3A%40%26%3D%2B%24%23
```

# 三、encodeURIComponent() 函数 与 encodeURI() 函数的区别?

前者假定它的参数是 URI 的一部分（比如协议、主机名、路径或查询字符串）

encodeURIComponent() 函数将转义用于分隔 URI 各个部分的标点符号。

个人理解：encodeURIComponent() 将URL字符串转为URL地址能识别从参数而不影响参数的传递，例如（/ : ? & = 等都是标准的URL传递参数的字符串，因此要全部转义），encode() 不影响URL 的整体结构，但是参数的非asscii码的值需要更改

```js
var set1 = ";,/?:@&=+$";  // 保留字符
var set2 = "-_.!~*'()";   // 不转义字符
var set3 = "#";           // 数字标志
var set4 = "ABC abc 123"; // 字母数字字符和空格

console.log(encodeURI(set1)); // ;,/?:@&=+$
console.log(encodeURI(set2)); // -_.!~*'()
console.log(encodeURI(set3)); // #
console.log(encodeURI(set4)); // ABC%20abc%20123 (空格被编码为 %20)

console.log(encodeURIComponent(set1)); // %3B%2C%2F%3F%3A%40%26%3D%2B%24
console.log(encodeURIComponent(set2)); // -_.!~*'()
console.log(encodeURIComponent(set3)); // %23
console.log(encodeURIComponent(set4)); // ABC%20abc%20123 (the space gets encoded as %20)
```

# 四、使用场景
1. 如果只是编码字符串，不和URL有半毛钱关系，那么用escape。
```js
```

2. 如果你需要编码整个URL，然后需要使用这个URL
用encodeURI
```js
encodeURI("http://www.cnblogs.com/season-huang/some other thing");
编码后会变为
"http://www.cnblogs.com/season-huang/some%20other%20thing";
空格被编码成了%20

```

用了encodeURIComponent
```js
encodeURI("http://www.cnblogs.com/season-huang/some other thing");
编码后会变为
"http%3A%2F%2Fwww.cnblogs.com%2Fseason-huang%2Fsome%20other%20thing"
看到了区别吗，连 "/" 都被编码了，整个URL已经没法用了。
```
3、当你需要编码URL中的参数的时候，那么encodeURIComponent是最好方法。

```js
var param = 'http://www.cnblogs.com/season-huang/' // param为参数
param = encodeURIComponent(param) // http%3A%2F%2Fwww.cnblogs.com%2Fseason-huang%2F
var url = 'http://www.cnblogs.com?next=' + param
console.log(url) // "http://www.cnblogs.com?next=http%3A%2F%2Fwww.cnblogs.com%2Fseason-huang%2F"
```

看到了把，参数中的 "/" 可以编码，如果用encodeURI肯定要出问题，因为后面的/是需要编码的。
特殊字符区分











