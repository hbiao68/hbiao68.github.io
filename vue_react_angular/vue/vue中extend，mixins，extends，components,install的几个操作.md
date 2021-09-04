@[toc]

# 一、参考
1. [Vue API](https://cn.vuejs.org/v2/api/#extends)

# 二、问题描述
最近工作要利用同事已经开发好的组件做修改，其中“地图”那块的业务逻辑非常的复杂，因此，将地图的初始化抽出来了，使用了Vue实例的extends属性，当一个页面出现多个地方要展现地图的时候，继承的威力就释放出来

由于涉及交互的地方非常多，一个页面也牵涉到大量的逻辑，因此Vue文件出现了大爆炸，尤其是牵涉到地图那块，因此，使用Vue实例的mixins将method等方法抽出来单独放到一个文件中，减少单个文件的巨大爆炸

看了Vue相关的文档，这种继承extend 、component、mixin 都分为全局API 和 实例属性，下面就根据全局API 和 实例属性这两类做了相关整理

# 三、全局API

## 3.1 [Vue.extend](https://cn.vuejs.org/v2/api/#Vue-extend)

使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

> 注意：data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数

### 3.1.2 快速入门
```js
// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{extendData}}</br>实例传入的数据为:{{propsExtend}}</p>',//template对应的标签最外层必须只有一个标签
  data: function () {
    return {
      extendData: '这是extend扩展的数据',
    }
  },
  props:['propsExtend']
})
// 创建 Profile 实例，并挂载到一个元素上。可以通过propsData传参.
new Profile({propsData:{propsExtend:'我是实例传入的数据'}}).$mount('#app-extend')
```

## 3.2 [Vue.component](https://cn.vuejs.org/v2/api/#Vue-component)
	
### 3.2.1 功能描述
1. `注册或获取全局组件`。
2. 注册还会`自动使用给定的 id 设置组件的名称`

作用：
通过 Vue.extend 生成的扩展实例构造器注册（命名）为一个全局组件,参数可以是Vue.extend()扩展的实例,也可以是一个对象(会自动调用extend方法)

本质：
与Vue.extend一样，`创建了一个Vue子类，只是多了一个全局唯一标识 key`

### 3.2.2 语法 Vue.component( id, [definition] )

* {string} id:    组件名的key

* {Function | Object} [definition]
  
  * 注册组件，传入一个扩展过的构造器
   > Vue.component('my-component', Vue.extend({ /* ... */ }))
  
  * 注册组件，传入一个选项对象 (自动调用 Vue.extend)
   >Vue.component('my-component', { /* ... */ })
  
  * 获取注册的组件 (始终返回构造器)
   > var MyComponent = Vue.component('my-component')

### 3.2.2 例子

```js
//1.创建组件构造器
var obj = {
  props: [],
  template: '<div><p>{{extendData}}</p></div>',//最外层只能有一个大盒子,这个和<tempalte>对应规则一致
  data: function () {
    return {
      extendData: '这是Vue.component传入Vue.extend注册的组件',
    }
  },
};

var Profile = Vue.extend(obj);

//2.注册组件方法一:传入Vue.extend扩展过得构造器
Vue.component('component-one', Profile)

//2.注册组件方法二:直接传入
Vue.component('component-two', obj)

//3.挂载
new Vue({
  el: '#app'
});

//获取注册的组件 (始终返回构造器)
var oneComponent=Vue.component('component-one');
console.log(oneComponent===Profile)//true,返回的Profile构造器
```



## 3.3 [Vue.mixin( mixin )](https://cn.vuejs.org/v2/api/#Vue-mixin)

全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为。不推荐在应用代码中使用。

# 四、属性

## 4.1 [extends](https://cn.vuejs.org/v2/api/#extends)
允许声明扩展另一个组件 (可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。

### 4.1.1 示例

```js
var CompA = { ... }

// 在没有调用 `Vue.extend` 时候继承 CompA
var CompB = {
  extends: CompA,
  ...
}
```

### 4.1.2 extends 和 mixins 属性混合一起使用
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.min.js"></script>
  </head>
  <body>
    <div id="app">
      <!-- <p>mixin的data值为:{{mixinData}}</p>
      <button @click="getSum()">点我触发getSum方法</button> -->
    </div>
  </body>
  <script>
    var extend = {
      template: `
         <div>
          <p> extend 的data值为:{{mixinData}}</p>
          <button @click="getSum()">点我触发getSum方法</button>
        </div>
      `,
      data: {
        mixinData: "我是extend的mixinData data",
        extendData: "我是extend的data"
      },
      created: function () {
        console.log("这是extend的created");
      },
      beforeDestroy() {
        console.log("这是extend的 beforeDestroy");
      },
      methods: {
        getSum: function () {
          console.log("这是extend的getSum里面的方法");
        },
      },
    };

    var mixin = {
      template: `
        <div>
          <p> mixin 的data值为:{{mixinData}}</p>
          <button @click="getSum()">点我触发getSum方法</button>
        </div>
      `,
      data: { mixinData: "我是mixin的data" },
      created: function () {
        console.log("这是mixin的created");
      },
      beforeDestroy() {
        console.log("这是mixin的 beforeDestroy");
      },
      methods: {
        getSum: function () {
          console.log("这是mixin的getSum里面的方法");
        },
      },
    };

    var vm = new Vue({
      template: `
      <div>
          <p> vue 的data值为:{{mixinData}}</p>
          <button @click="getSum()">点我触发getSum方法</button>
        </div>
      `,
      el: "#app",
      data: { mixinData: "我是vue实例的data" },
      created: function () {
        console.log("这是vue实例的created");
      },
      beforeDestroy() {
        console.log("这是vue实例的 beforeDestroy");
      },
      methods: {
        getSum: function () {
          console.log("这是vue实例里面getSum的方法");
        },
      },
      mixins: [mixin],
      extends: extend,
    });
  </script>
</html>
```
结论
1. `extends执行顺序为:extends>mixins>mixinTwo>created`
2. 定义的属性覆盖规则和mixins一致
> Vue定义的属性和方法  覆盖 mixins  覆盖 extends
> 生命周期函数是先执行 extends   再执行 mixins  再执行  Vue实例的

## 4.2 components
注册一个局部组件，即包含 Vue 实例可用组件的哈希表。

### 4.2.1 示例
```js
//1.创建组件构造器
var obj = {
  props: [],
  template: '<div><p>{{extendData}}</p></div>',//最外层只能有一个大盒子,这个和<tempalte>对应规则一致
  data: function () {
    return {
      extendData: '这是component局部注册的组件',
    }
  },
};

var Profile = Vue.extend(obj);

//3.挂载
new Vue({
  el: '#app',
  components:{
    'component-one':Profile,
    'component-two':obj
  }
});
```

## 4.3 [mixins](https://cn.vuejs.org/v2/api/#mixins)
mixins 选项`接收一个混入对象的数组`。这些混入对象可以像正常的实例对象一样包含实例选项，这些选项将会被合并到最终的选项中，`使用的是和 Vue.extend() 一样的选项合并逻辑`。也就是说，`如果你的混入包含一个 created 钩子，而创建组件本身也有一个，那么两个函数都会被调用`。

`Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用`。
    
### 4.3.1 示例
```js
var mixin={
  data:{mixinData:'我是mixin的data'},
  created:function(){
    console.log('这是mixin的created');
  },
  methods:{
    getSum:function(){
      console.log('这是mixin的getSum里面的方法');
    }
  }
}

var mixinTwo={
  data:{mixinData:'我是mixinTwo的data'},
  created:function(){
    console.log('这是mixinTwo的created');
  },
  methods:{
    getSum:function(){
      console.log('这是mixinTwo的getSum里面的方法');
    }
  }
} 

var vm=new Vue({
  el:'#app',
  data:{mixinData:'我是vue实例的data'},
  created:function(){
    console.log('这是vue实例的created');
  },
  methods:{
    getSum:function(){
      console.log('这是vue实例里面getSum的方法');
    }
  },
  mixins:[mixin,mixinTwo]
})

//打印结果为:
这是mixin的created
这是mixinTwo的created
这是vue实例的created
这是vue实例里面getSum的方法
```
结论
`Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用。`
1. mixins执行的顺序为`mixins > mixinTwo > created` (vue实例的生命周期钩子); 
2. 选项中数据属性如data,methods,后面执行的回覆盖前面的
3. 生命周期钩子都会执行


# 五、 Vue.extend 和 Vue.component component 的区别

1. Vue.component component两者都是需要先进行组件注册后，然后在 template 中使用注册的标签名来实现组件的使用。Vue.extend 则是编程式的写法

2. 关于组件的显示与否，需要在父组件中传入一个状态来控制 或者 在组件外部用 v-if/v-show 来实现控制，而 Vue.extend 的显示与否是手动的去做组件的挂载和销毁。

3. Vue.component component 在组件中需要使用 slot 等自定义UI时更加灵活，而 `Vue.extend 由于没有 template的使用`，没有slot 都是通过 props 来控制UI，更加局限一些。

# 六、插件开发（Vue.use && install）

1. Vue.js 的插件应该暴露一个 install 方法。
2. 这个方法的第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象：

## 6.1 示例
```js
 var MyPlugin = {};
  MyPlugin.install = function (Vue, options) {
    // 2. 添加全局资源,第二个参数传一个值默认是update对应的值
    Vue.directive('click', {
      bind(el, binding, vnode, oldVnode) {
        //做绑定的准备工作,添加时间监听
        console.log('指令my-directive的bind执行啦');
      },
      inserted: function(el){
      //获取绑定的元素
      console.log('指令my-directive的inserted执行啦');
      },
      update: function(){
      //根据获得的新值执行对应的更新
      //对于初始值也会调用一次
      console.log('指令my-directive的update执行啦');
      },
      componentUpdated: function(){
      console.log('指令my-directive的componentUpdated执行啦');
      },
      unbind: function(){
      //做清理操作
      //比如移除bind时绑定的事件监听器
      console.log('指令my-directive的unbind执行啦');
      }
    })

    // 3. 注入组件
    Vue.mixin({
      created: function () {
        console.log('注入组件的created被调用啦');
        console.log('options的值为',options)
      }
    })

    // 4. 添加实例方法
    Vue.prototype.$myMethod = function (methodOptions) {
      console.log('实例方法myMethod被调用啦');
    }
  }

  //调用MyPlugin
  Vue.use(MyPlugin,{someOption: true })

  //3.挂载
  new Vue({
    el: '#app'
  });

```

# 七、结论

1. Vue.extend和Vue.component是为了创建构造器和组件; 

2. mixins和extends是为了拓展组件; 

3. install是开发插件

4. Vue.extend>Vue.component>extends>mixins

