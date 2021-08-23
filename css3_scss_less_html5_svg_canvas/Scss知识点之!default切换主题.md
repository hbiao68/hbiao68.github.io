

@[toc]

# 一、参考
1. [sass官网——默认变量值](https://www.sass.hk/guide/)
2. [less官网——默认变量值](https://less.bootcss.com/features/#variables-default-variables)


# 二、scss 中的!default

## 2.1 场景说明
假如你写了一个可被他人通过@import导入的sass库文件，你可能希望导入者可以定制修改sass库文件中的某些值


## 2.2作用
它很像css属性中!important标签的对立面，不同的是!default用于变量
含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

## 2.3 例子
> scss变量是使用 $标识

1. 如果变量之前没有赋值，则使用默认值：
```css
/* 如果之前没有赋值,则使用默认值 */
$const: "hello" !default;

div{
    const: $const;
}
```
编译后
```css
div {
const: "hello"; 
}
```

2. 如果在此之前已经赋值，那就不再使用默认值：
```css
/* 如果之前已经赋值,则不再使用默认值 */
$const: "Hi";
$const: "hello" !default;

div{
    const: $const;
}
```
编译后
```css
div {
  const: "Hi"; 
}
```

# 三、less中的默认值
> less变量是使用 @标识

```css
// library
@base-color: green;
@dark-color: darken(@base-color, 10%);

// use of library
@import "library.less";
@base-color: red;
```

`This works fine because of Lazy Loading - @base-color is overridden and @dark-color is a dark red.`