@[toc]

# 一、参考
1. 



# 二、why 模块化

## 2.1 JavaScript设计缺陷

1. 比如var定义的变量作用域问题；

2. 比如JavaScript的面向对象并不能像常规面向对象语言一样使用class；

3. 比如JavaScript没有模块化的问题；

## 2.2 随着前端和JavaScript的快速发展，JavaScript代码变得越来越复杂，不能满足需求

1. ajax的出现，前后端开发分离，意味着后端返回数据后，我们需要通过JavaScript进行前端页面的渲染；

2. SPA的出现，前端页面变得更加复杂：包括前端路由、状态管理等等一系列复杂的需求需要通过JavaScript来实现；

3. 包括Node的实现，JavaScript编写复杂的后端程序，没有模块化是致命的硬伤；

## 2.3 没有模块化的JavaScript的模块化方案

### 2.3.1 全局函数

#### 2.3.1.1 示例
// 小明开发了aaa.js文件，代码如下（当然真实代码会复杂的多）：
```js
var flag = true;

if (flag) {
  console.log("aaa的flag为true")
}
```

// 小丽开发了bbb.js文件，代码如下：
```js
var flag = false;

if (!flag) {
  console.log("bbb使用了flag为false");
}
```

// 小明又开发了ccc.js文件
```js
if (flag) {
  console.log("使用了aaa的flag");
}
```

使用路径引入
```js
<script src="./aaa.js"></script>
<script src="./bbb.js"></script>
<script src="./ccc.js"></script>
```

> 如果每个文件都有上千甚至更多的代码，而且有上百个文件，你可以一眼看出来flag在哪个地方被修改了吗？

#### 2.3.1.2 缺点
1. ”污染”了全局变量，无法保证不与其它模块发生变量名冲突

2. 没有模块的划分，只能人为的认为它们属于一个模块，但是程序并不能区分哪些函数是同一个模块


### 2.3.2 将函数封装到对象命名空间下

1. 从代码级别可以明显的区分出哪些函数属于同一个模块

2. 从某种程度上解决了变量命名冲突的问题，但是并不能从根本上解决命名冲突

3. 会暴露所有的模块成员，内部状态可以被外部改写，不安全

4. 命名空间越来越长

### 2.3.3 立即函数调用表达式(IIFE，Immediately Invoked Function Expression)

1. 将模块封装为立即执行函数形式，将公有方法，通过在函数内部返回值的形式向外暴露

2. 会有人强调职责单一性，不要与程序的其它部分直接交互。比如当使用到第三方依赖时，通过向匿名函数注入依赖项的形式，来保证模块的独立性，还使模块之间的依赖关系变得明显

#### 2.3.3.1 示例
```js
// aaa.js
const moduleA = (function () {
  var flag = true;

  if (flag) {
    console.log("aaa的flag为true")
  }

  return { flag: flag }
})();

// bbb.js
const moduleB = (function () {
  var flag = false;

  if (!flag) {
    console.log("bbb使用了flag为false");
  }
})();

// ccc.js
const moduleC = (function() {
  const flag = moduleA.flag;
  if (flag) {
    console.log("使用了aaa的flag");
  }
})();
```

#### 2.3.3.2 缺点
第一，`我必须记得每一个模块中返回对象的命名，才能在其他模块使用过程中正确的使用`；

第二，代码写起来混乱不堪，每个文件中的代码都需要包裹在一个匿名函数中来编写；

第三，`在没有合适的规范情况下，每个人、每个公司都可能会任意命名、甚至出现模块名称相同的情况`


# 三、CommonJS规范

## 3.1 概念
* CommonJS是一个规范
* Node是CommonJS在服务器端一个具有代表性的实现
* Browserify是CommonJS在浏览器中的一种实现；
* webpack打包工具具备对CommonJS的支持和转换（后面会讲到）；

## 3.2 Node模块化语法

### 3.2.1 Node.js中，模块分为两类

* 系统核心模块(原生模块)，node自带
  * fs(file system)：与文件系统交互
  * http：提供http服务器功能
  * os：提供了与操作系统相关的实用方法和属性
  * path：处理文件路径
  * querystring：解析url查询字符串
  * url：解析url
  * util：提供一系列实用小工具
  * Buffer
  * 等等很多，见官方文档

* 文件模块，也称自定义模块。用路径加载

### 3.2.2 exports导出

exports是一个对象，我们可以在这个对象中添加很多个属性，添加的属性会导出

module.exports和exports有什么关系或者区别呢？
* CommonJS中是没有module.exports的概念
* 但是为了实现模块的导出，Node中使用的是Module的类(提供了一个Module构造函数)，每一个模块都是Module的一个实例，也就是module
* module才是导出的真正实现者；
* 在Node中真正用于导出的其实根本不是exports，而是module.exports
* 只是为了实现CommonJS的规范，也为了使用方便，Node为每个模块提供了一个exports对象，让其对module.exports有一个引用而已。
* 相当于在每个模块头部，有这样一行命令：var exports = module.exports;

### 3.2.3 require

#### 3.2.3.1 require的加载原理

* CommonJS是同步加载。模块加载的顺序，按照其在代码中出现的顺序
* require命令第一次加载模块时，会执行整个模块(脚本文件)中的js代码，返回该模块的module.exports接口数据。会在内存生成一个该模块对应的module对象
* 以后需要用到这个模块的时候，就会到exports属性上面取值。
* 模块被多次引入时（多次执行require命令），CommonJS 模块只会在第一次加载时运行一次，以后再加载，会去缓存中取出第一次加载时生成的module对象并返回module.exports。除非手动清除系统缓存

#### 3.2.3.1 require的查找规则

Node.js会通过同步阻塞的方式看这个路径是否存在。依次尝试，直到找到为止，如果找不到，报错

优先从缓存加载：common.js规范：载后，再次加载时，去缓存中取module.exports 

* X是一个核心模块，比如path、http。直接返回核心模块，并且停止查找

* X是以 ./ 或 ../ 或 /（根目录）开头的,将X当做一个文件在对应的目录下查找)
  * 如果有后缀名，按照后缀名的格式查找对应的文件
  * 如果没有后缀名
    * 直接查找文件X
      * 查找X.js文件：当做JavaScript脚本文件解析
      * 查找X.json文件：以JSON格式解析
        * 如果是加载json文件模块，最好加上后缀.json，能稍微的提高一点加载的速度。
        * json文件Node.js也是通过fs读文件的形式读取出来的，然后通过JSON.parse()转换成一个对象
      * 查找X.node文件：以编译后的二进制文件解析。.node文件通常是c/c++写的一些扩展模块
    * 没有找到对应的文件，将X作为一个目录
      * 查找X/index.js文件
      * 查找X/index.json文件
      * 查找X/index.node文件

* 直接是一个X（没有路径），并且X不是一个核心模块
  * 从当前 package 的 node_modules 里面找，找不到就到当前 package 目录上层 node_modules 里面取... 一直找到全局 node_modules 目录。
  * 有 package.json，就根据它的 main 字段找到 js 文件。如果没有 package.json，那就默认取文件夹下的 index.js。
  * 如果上面的路径中都没有找到，那么报错：not found


# 四、ES6 Module

## 4.1 ES6 Module的优势
* 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高
* 使得静态分析成为可能。
* 不再需要UMD模块格式了，将来服务器和浏览器都会支持 ES6 模块格式。
* 不再需要对象作为命名空间（比如Math对象），未来这些功能可以通过模块提供。

## 4.2 浏览器中加载js文件
1. `<script type="application/javascript"> // code </script>`
默认情况下，浏览器是同步加载 JavaScript 脚本，即渲染引擎遇到<script>标签就会停下来，等到执行完脚本，再继续向下渲染
如果是外部脚本，还必须加入脚本下载的时间。

2. `<script src="path/to/myModule.js" defer></script>`
defer要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），才会执行
如果有多个defer脚本，会按照它们在页面出现的顺序加载

3. `<script src="path/to/myModule.js" async></script>`
async一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染
多个async脚本是不能保证加载顺序的。

## 4.3 加载ES6 Module

ES6 模块也允许内嵌在网页中，语法行为与加载外部脚本完全一致。
```js
<script type="module">
import utils from "./utils.js";

// other code
</script>
```

* 同一个模块如果加载多次，将只执行一次。
* 模块之中，顶层的this关键字返回undefined，而不是指向window。也就是说，在模块顶层使用this关键字，是无意义的。
* 模块之中，可以使用import命令加载其他模块（.js后缀不可省略，需要提供绝对 URL 或相对 URL），也可以使用export命令输出对外接口。
* 模块脚本自动采用严格模式，不管有没有声明use strict。
* 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见


1. `<script type="module" src="./foo.js"></script>`
等价于
`<script type="module" src="./foo.js" defer></script>`
`如果网页有多个 <script type="module">，它们会按照在页面出现的顺序依次执行。`

2. `<script type="module" src="./foo.js" async></script>`
这时只要加载完成，渲染引擎就会中断渲染立即执行。执行完成后，再恢复渲染
一旦使用了此属性，<script type="module">就不会按照在页面出现的顺序执行，而是只要该模块加载完成，就执行该模块。

    
## 4.4 export
### 4.4.1 导出方式

方式一：分别导出。
> export <decl>

方式二：统一导出
> export {}

方式三: 标识符 起一个别名
> export {<> as <>}

### 4.4.2 特性说明
* export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。
* export命令可以出现在模块的任何位置，只要处于模块顶层就可以。
* 不同的模块中，加载这个模块，得到的都是同一个实例
* 一个模块中：export <decl>、export {}、export {<> as <>}都是可以出现0-n次的

## 4.5 import

### 4.5.1 导入方式
方式一：选择导入。import {标识符列表} from '模块'；
> import 'lodash'; 
> import语句会执行所加载的模块，因此可以有下面的写法。

方式二：导入时给标识符起别名：
> import {<> as <>} from ''

方式三：整体导入
> import * as <> from ''

### 4.5.2 import 特性
	
1. import语句会执行所加载的模块。如果同一个模块被加载多次，那么模块里的代码只执行一次。
2. import导入为只读
```js
import { name } from './modules/foo.js';
name = "mod"; // Syntax Error : 'name' is read-only;
```
3. import后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径，后缀名不能省略
4. import命令具有提升效果，会提升到整个模块的头部，首先执行
5. import中不能使用表达式和变量
```js
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

## 4.6 export default
### 4.6.1 特点
1. 默认导出export时可以不需要指定名字；
2. 在导入时不需要使用 {}，并且可以自己来指定名字；
3. 它也方便我们和现有的CommonJS等规范相互操作；
4. 导出与导入格式——也是可以导出变量、函数、类的。

### 4.6.2 export default的本质

export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。

```js
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};  // 等同于 export default add;

// app.js
import { default as foo } from 'modules'; // 等同于 import foo from 'modules';
```

### 4.6.3 export default VS. export
1. export default命令只能使用一次。

2. export是没有限制的。export <decl>、export {}、export {<> as <>}都是可以出现0-n次

### 4.6.4 从一个模块中导入的内容，我们希望再直接导出出去，这个时候可以使用export和import的结合，写成一行

#### 4.6.4.1 why
在开发和封装一个功能库时，通常我们希望将暴露的所有接口放到一个文件中；
这样方便指定统一的接口规范，也方便阅读；
这个时候，我们就可以使用export和import结合使用；

#### 4.6.4.2 案例    

```
export { sum } from './bar.js';
```

1. 接口改名
```js
export { sum as barSum } from './bar.js';
```
2. 整体导入和导出
```js
export * from './bar.js';
```

3. 默认接口
```js
export { default } from 'foo'
```
	
4. 具名接口改为默认接口的写法

```js
export { es6 as default } from './someModule';
```

等价于

```js
import { es6 } from './someModule';
export default es6;
```

5. 默认接口也可以改名为具名接口
```js
export { default as es6 } from './someModule';
```
	
6. 所有接口改为具名接口

```js
export * as ns from "mod";
```

等价于

```js
import * as ns from "mod";
export {ns};
```

## 4.7 import()

### 4.7.1 语法
`import("加载模块的位置").then(function(){})`

参数指定要加载的模块的位置

返回一个 Promise 对象

例子
```js
import(`./section-modules/${someVariable}.js`)
.then(module => {     // 加载模块成功以后，这个模块会作为一个对象，当作`then`方法的参数.
//.then({export1, export2} => {     // 可以使用对象解构赋值的语法，获取输出接口。
//.then({default: theDefault} => {  // 如果是default，那么需要解构重命名
  
  module.loadPageInto(main); // module.default来使用默认导出
})
.catch(err => {
  main.textContent = err.message;
});
```
```

### 4.7.1 适用场合
1. 按需加载
import()可以在需要的时候，再加载某个模块。比如放在click事件的监听函数之中，只有用户点击了按钮，才会加载这个模块
2. 条件加载
import()可以放在if代码块，根据不同的情况，加载不同的模块。
3. 动态的模块路径
import()允许模块路径动态生成。

# 五、ES6  vs   Commonjs

1. CommonJS 模块输出的是一个值的拷贝(module.exports的浅拷贝)，ES6 模块输出的是值的引用

2. CommonJS 模块是运行时加载，ES6 模块是编译(解析)时加载

3. CommonJS 模块的require()是同步加载模块，ES6 模块的import命令是异步加载，有一个独立的模块依赖的解析阶段。



