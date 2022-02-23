从Vue.extend API了解到Object.create实现继承

@[tac]

# 一、参考
1. [Object.create() MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
2. [Object.prototype.constructor MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)
3. [Object.create()和深拷贝](https://www.cnblogs.com/zhilin/p/11402064.html)



# 二、问题描述
在工作中，发现有同事使用Vue 的 extend 和 mixin 属性，发现能够简化代码，发现自己对 extend、mixin 的底层逻辑不是很清晰，因此看看源码，借此深入了解Vue

```js
Vue.extend = function (extendOptions: Object): Function {
    extendOptions = extendOptions || {}
    const Super = this
    const SuperId = Super.cid
    const cachedCtors = extendOptions._Ctor || (extendOptions._Ctor = {})
    if (cachedCtors[SuperId]) {
      return cachedCtors[SuperId]
    }

    const name = extendOptions.name || Super.options.name
    if (process.env.NODE_ENV !== 'production' && name) {
      validateComponentName(name)
    }
    // 返回新的Vue 对象
    const Sub = function VueComponent (options) {
      this._init(options)
    }
    // Sub 继承了 Vue 类
    // 继承Super，如果使用Vue.extend，这里的Super就是Vue
    Sub.prototype = Object.create(Super.prototype)
    Sub.prototype.constructor = Sub
    Sub.cid = cid++
    // 将extend的option 和 Vue.options 合并
    Sub.options = mergeOptions(
      Super.options,
      extendOptions
    )
    Sub['super'] = Super

    // For props and computed properties, we define the proxy getters on
    // the Vue instances at extension time, on the extended prototype. This
    // avoids Object.defineProperty calls for each instance created.
    if (Sub.options.props) {
      initProps(Sub)
    }
    if (Sub.options.computed) {
      initComputed(Sub)
    }

    // allow further extension/mixin/plugin usage
    // 继承Vue的global-api
    Sub.extend = Super.extend
    Sub.mixin = Super.mixin
    Sub.use = Super.use

    // create asset registers, so extended classes
    // can have their private assets too.
    // 继承assets的api，比如注册组件，指令，过滤器
    // export const ASSET_TYPES = ['component','directive','filter']
    ASSET_TYPES.forEach(function (type) {
      Sub[type] = Super[type]
    })
    // enable recursive self-lookup
    if (name) {
      Sub.options.components[name] = Sub
    }

    // keep a reference to the super options at extension time.
    // later at instantiation we can check if Super's options have
    // been updated.
    Sub.superOptions = Super.options
    Sub.extendOptions = extendOptions
    Sub.sealedOptions = extend({}, Sub.options)

    // cache constructor
    cachedCtors[SuperId] = Sub
    return Sub
  }
}
```

最新在学习Vue.extend 的源码，本质上该方法是改变了Vue的构造函数，继承了Vue类;
1. 继承Vue类的主要方法是Object.create() 实现原型链的扩展
2. Sub.prototype.constructor 重置Sub 的构造函数

```js
// Sub 继承了 Vue 类
// 继承Super，如果使用Vue.extend，这里的Super就是Vue
Sub.prototype = Object.create(Super.prototype)
Sub.prototype.constructor = Sub
```

# 三、Object.create()

## 3.1 Object.create(proto，[propertiesObject])  语法
1. 概念：Object.create()`方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__`
2. 参数
proto：新创建对象的原型对象。
propertiesObject：可选。需要传入一个对象，该对象的属性类型参照Object.defineProperties()的第二个参数。如果该参数被指定且不为 undefined，该传入对象的自有可枚举属性(即其自身定义的属性，而不是其原型链上的枚举属性)将为新创建的对象添加指定的属性值和对应的属性描述符。
3. 返回值：一个新对象，带着指定的原型对象和属性。

## 3.2 快速入门

```js
const person = {
  isHuman: false,
  printIntroduction: function() {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = 'Matthew'; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
// expected output: "My name is Matthew. Am I human? true"
```


```js
```

## 3.3 单继承
```js
// Shape - 父类(superclass)
function Shape() {
  this.x = 0;
  this.y = 0;
}

// 父类的方法
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};

// Rectangle - 子类(subclass)
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// 子类续承父类
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();

console.log('Is rect an instance of Rectangle?',
  rect instanceof Rectangle); // true
console.log('Is rect an instance of Shape?',
  rect instanceof Shape); // true
rect.move(1, 1); // Outputs, 'Shape moved.'
```


## 3.4 多继承

```js
function MyClass() {
     SuperClass.call(this);
     OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
// 重新指定constructor
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
     // do a thing
};

```
> 使用 Object.assign 扩展 prototype

## 3.5 new Object()  VS Object.create() 区别

### 3.5.1 new Object()

> new Object() 通过构造函数来创建对象, 添加的属性是在自身实例下。

```js
// new Object() 方式创建
var a = {  rep : 'apple' }
var b = new Object(a)
console.log(b) // {rep: "apple"}
console.log(b.__proto__) // {}
console.log(b.rep) // {rep: "apple"}
```


### 3.5.2 Object.create()
> Object.create() es6创建对象的另一种方式，可以理解为继承一个对象, 添加的属性是在原型下。

```js
// Object.create() 方式创建
var a = { rep: 'apple' }
var b = Object.create(a)
console.log(b)  // {}
console.log(b.__proto__) // {rep: "apple"}
console.log(b.rep) // {rep: "apple"}
```


# 四、Object.prototype.constructor

## 4.1 功能说明
返回创建实例对象的 Object 构造函数的引用
`此属性的值是对函数本身的引用`，而不是一个包含函数名称的字符串。
对原始类型来说，如1，true和"test"，该值只可读

## 4.2 快速入门
```js
function Tree(name) {
  this.name = name;
}

var theTree = new Tree("Redwood");
console.log( "theTree.constructor is " + theTree.constructor );
console.log(theTree.constructor === Tree) // true
Object.create 实现继承的时候，将函数的方法赋值给 prototype.constructor
```











































































