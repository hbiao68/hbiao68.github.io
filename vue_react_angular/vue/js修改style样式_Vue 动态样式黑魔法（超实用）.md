@[toc]

# 一、参考
1. [js修改style样式_Vue 动态样式黑魔法（超实用）](https://blog.csdn.net/weixin_39927993/article/details/110470333)
2. [CSS 变量教程   阮一峰](https://www.ruanyifeng.com/blog/2017/05/css-variables.html)


# 二、问题描述
工作中使用 elementUI 的scrollbar 组件，例如 `<el-scrollbar wrap-class="demo-scrollbar-wrap-2">`，只能让 `wrap-class`设置具体的类名，**当浏览器窗口发生变化的时候，类名没有改变，则对应的样式也没有改变，导致scroll无法做到“自适应窗口变化”的效果**



众所周知，Vue 中动态绑定样式是用 :style，或者是动态绑定 :class class，不同的 class 样式提前写好且不一样。
但是如果是 ::after伪元素或者要改变的样式用 js 计算很复杂但是用 CSS 计算很简单的话，这种方法就略显得麻烦。有没有什么办法能用 Vue 直接改变 CSS，而不用这一套绑定的办法呢。答案是有的！

​这里给出两个实现方案。

# 三、第一个方案——动态 style 标签
其实早在 Vue 0.x 和 1.x 版本这样做一度很流行（和这种升级的方案略有不同）

## 第一个例子
```html
<template>
  <div>
    <component is="style">
      .foo[data-id="{{ uniqueId }}"] {
        color: {{ color }};
      }
      .foo[data-id="{{ uniqueId }}"] .bar {
        text-align: {{ align }}
      }
    </component>
    <div class="foo" :data-id="uniqueId">
      <div class="bar">
          hello world
      </div>
    </div>
  </div>
</template>
<script>
export default {
  computed: {
    uniqueId() {
      return 一个独一无二的id; // 是因为这样生成的 style 没有 scoped，别的组件也能使用这个样式
    },
    color() {
      return someCondition ? 'red' : '#000';
    },
    align() {
      return someCondition ? 'left' : 'right';
    }
  }
}
</script>
```

## 第二个例子
```html
<div id="app">
  <v-style>
    .{{ className }} {
      background: {{ bgColor }};
      position: relative;
    }
 
    .{{ className }}:hover {
      color: {{ hoverColor }};
    }
    .{{ className }}::after {
      content: '';
      display: block;
      height: 40px;
      width: 40px;
      border: 1px solid black;
      border-radius: 50%;
      position: absolute;
      top: 100%;
    }
  </v-style>
    <div class="temp">
 
    </div>
</div>
<script>
Vue.component('v-style', {
  render: function (createElement) {
    return createElement('style', this.$slots.default)
  }
});
export default {
    data () {
        return {
            className: "temp",
            hoverColor: "yellow",
            bgColor: "blue"
        }
    },
    computed () {
        // 这里和上面一样，所以略去生成 uniqueId 的过程
    }
}
</script>
```

# 四、第二个方案——CSS 变量
上面的方案之所以被 Vue 官方不赞成，是因为 Vue 在每次渲染的时候会把每个组件的 style 标签单独拎出来，比较耗费性能。所以有没有一个直观的方案就是 Vue 直接操纵 CSS 呢，有的，借助 CSS 变量就可以。

## 快速入门 CSS 变量 ，var() 函数
1. 声明变量的时候，变量名前面要加两根连词线（--）。
2. var()函数用于读取变量。
3. var()函数还可以使用第二个参数，表示变量的默认值。如果该变量不存在，就会使用这个默认值。
```css
color: var(--foo, #7F583F);
```

**快速入门案例**

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>菜鸟教程(runoob.com)</title>
    <style>
      :root {
        --main-bg-color: yellow;
      }

      #div1 {
        background-color: var(--main-bg-color);
        padding: 5px;
      }

      #div2 {
        background-color: var(--main-bg-color);
        padding: 5px;
      }

      #div3 {
        background-color: var(--main-bg-color);
        padding: 5px;
      }
    </style>
  </head>
  <body>
    <h1>var() 函数</h1>
    <div id="div1" style="--main-bg-color: red;">菜鸟教程 - 学的不仅是技术，更是梦想！</div>
    <br />
    <div id="div2">菜鸟教程 - 学的不仅是技术，更是梦想！</div>
    <br />
    <div id="div3">菜鸟教程 - 学的不仅是技术，更是梦想！</div>
  </body>
</html>
```

## Vue 修改CSS变量案例

```html
<template>
    <div class="test">
        <span :style="spanStyle" class="span1">hello world</span>
        <br>
        <span :style="{'--width': widthVar}" class="span2">hello earth</span>
    </div>
</template>
<script>
export default {
    data() {
        return {
            spanStyle: {
                "--color": "red"
            },
            widthVar: "100px"
        };
    }
}
</script>
<style scoped>
    .span1 {
        color: var(--color);
    }
    .span2 {
        text-align: center;
        position: relative;
        width: var(--width);
    }
    .span2::after {
        content: '';
        display: block;
        position: absolute;
        left: 100%; 
        width: var(--width);
        height: var(--width);
        border-radius: 50%;
        border: 2px solid black;      
    }
</style>
```

# 五、总结
第二种方案虽然用起来直观，但不是说第一种方案就一无是处了。第一种方案在用来动态定义全局 CSS 变量的时候很好用。例子如下

```html
<component is="style">
    :root {
        --bg-color: {{bgColor}};
        --box-size: {{boxSize}};
    }
</component>
<script>
export default {
  data () {
      return {
          bgColor: "white",
          boxSize: "30px"
      }
  }
}
</script>
```
将二者相辅相成并且结合 Vue 原有的:class和:style，相信大家能写出更优雅的 Vue 代码。
