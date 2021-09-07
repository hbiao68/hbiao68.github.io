@[toc]

#  一、	参考
1. [自定义组件的 v-model](https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model)
2. [Vue 表单双向绑定](https://cn.vuejs.org/v2/guide/forms.html#%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95)

# 二、v-model 语法

## 2.1 参考
[Vue 表单双向绑定](https://cn.vuejs.org/v2/guide/forms.html#%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95)

## 2.2 v-model 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：
1. **text 和 textarea 元素使用 value property 和 input 事件；**

2. **checkbox 和 radio 使用 checked property 和 change 事件；**

3. **select 字段将 value 作为 prop 并将 change 作为事件。**

## 2.3 checkbox

1. 绑定到布尔值
```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

2. 值绑定
```html
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
<br>
<span>Checked names: {{ checkedNames }}</span>
```

3. 自定义绑定值
```js
<input
	type="checkbox"
	v-model="toggle"
	true-value="yes"
	false-value="no"
>
// 当选中时
vm.toggle === 'yes'
// 当没有选中时
vm.toggle === 'no'
```

## 2.4 radio

1. 值绑定
```html
<div id="example-4">
	<input type="radio" id="one" value="One" v-model="picked">
	<label for="one">One</label>
	<br>
	<input type="radio" id="two" value="Two" v-model="picked">
	<label for="two">Two</label>
	<br>
	<span>Picked: {{ picked }}</span>
</div>
```

> 选中是a的值是 One或者是 Two

2. 自定义选中值
```html
<input type="radio" v-model="pick" v-bind:value="a">
```

> 当选中时 `vm.pick === vm.a`

## 2.5 修饰符

### .lazy
转为在 change 事件_之后_进行同步，即在“change”时而非“input”时更新

```html
<input v-model.lazy="msg">
```


### .number
自动将用户的输入值转为数值类型

```html
<input v-model.number="age" type="number">
```

`因为即使在 type="number" 时，HTML 输入元素的值也总会返回字符串`。

如果这个值无法被 parseFloat() 解析，则会返回原始的值。

### .trim
自动过滤用户输入的首尾空白字符

```html
<input v-model.trim="msg">
```


# 三、组件上的 v-model

## 3.1 v-model原理
1. **触发的是input事件或者是change事件，根据input类型来区分**

2. **将表单变化之后的值赋值给当前表单的值，最终实现双向绑定**

```html
<template>
    <div class="MyModelComp">
        <div>
            <input :value="myinputValue" type="text"
                   @input="userInputAction"/>
        </div>
        <div>
            <input v-bind:value="myinputValue" v-on:input="userInputAction" />
        </div>
    </div>
</template>

<script>
  export default {
    data: function () {
      return {
        myinputValue: '',
      }
    },
    methods: {
	  // 模拟v-modal 做的事情
      userInputAction: function (event) {
        console.log(event.target.value)
        this.myinputValue = event.target.value;
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
    .MyModelComp {
    }
</style>
```


## 3.2 组件接收v-model的prop参数

### 3.2.1 默认参数value和默认input事件

1. 自定义组件
```html
<template>
    <!-- 最外层只能有一个组件，不能出现多个 -->
    <!-- 最外层class命名规则 工程-文件路径-文件名  -->
    <div class="">
        {{newData}}
        <button @click="changeData">改变数据</button>
    </div>
</template>

<script>
    export default {
        props:{
            value: "", // 默认接收v-modal的属性值
        },
        data: function (){
            return {
                newData:"",
            }
        },
        mounted: function(){
            debugger
            console.log("this.value : " + this.value);
        },
        methods: {
            changeData : function (){
                debugger
                this.newData = new Date().getTime() + "----" + this.value;
                // 要暴露Input事件出去，修改组件外部的变量，从而影响到组件接收的值保持一致
                this.$emit("input",this.newData);
            }
        },
        watch: {
            input:function (newValue, oldValue) {
                console.log("watch input" + newValue);
            }
        }
    }
</script>
```

2. 使用自定义组建
```html
<template>
    <!-- 最外层只能有一个组件，不能出现多个 -->
    <!-- 最外层class命名规则 工程-文件路径-文件名  -->
    <div class="demo-extendPrototype-stringExtend">
        <vmodel-comp
            v-model="userinput"
        ></vmodel-comp>
        {{userinput}}
    </div>
</template>

<script>
    import vmodelComp from "./vmodelComp.vue";
    export default {
        mounted: function(){
        },
        data: function (){
            return {
                userinput:"huangbiao"
            }
        },
        components:{
            vmodelComp
        }
    }
</script>
```

### 3.2.2 自定义值参数和事件（如radio、checkbox）

* 使用model 属性重新定义 变量 和 事件 
  * prop指明属性的名字
  * event定义父组件定义的事件

### 3.2.2.1 官网语法介绍
```js
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```
使用组件
```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```

### 3.2.2.2 自定义组件
```html
<template>
    <div class="MyModelComp">
        <div>自定义组件v-model</div>
        <div>
            <input type="text" :value="inputValue" @input="inputChnageAction($event)"/>
        </div>
        <div>
            <ul>
                <li>组件的model需要定义“事件名”和 “变量名”</li>
                <li>“变量名”必须在props中定义</li>
                <li>组件内部不能直接使用v-model响应事件，否则会出现“死循环”报错</li>
            </ul>
        </div>
    </div>
</template>

<script>
  export default {
    model: {
      prop: 'inputValue',  // 组件v-model定义的名字, 与username对应
      event: 'huangbiao'   // 父组件定义的事件名，抛出该事件，父组件就能接收
    },
    props: {
      inputValue: String,
    },
    methods: {
      inputChnageAction: function (event) {
        // 抛出父组件的值，将改变的值传递给父组件
        this.$emit("huangbiao", event.target.value);
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
    .MyModelComp {
    }
</style>
```