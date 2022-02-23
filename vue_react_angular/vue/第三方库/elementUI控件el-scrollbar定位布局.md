@[tac]

# 一、问题描述
每次使用`el-scrollbar`组件显示滚动条，都需要设置`wrap-class`样式的具体高度，如果有细微变化调整不灵活

解决办法：使用一个div 控件将 el-scrollbar 组件包裹起来，并设置为 `position: relative;`相对定位，`wrap-class` 设置绝对定位，`撑满父容器`，因此只需要设置父容器的高宽度位置即可


# 二、案例
```html
<template>
  <div class="huangbiao">
    <div class="huangbiao--header">header</div>
    <div class="huangbiao--content">
      <el-scrollbar wrap-class="hb-scroll">
        <ul>
          <li v-for="i in 100" :key="i">{{ i }} ----------------------------------------
            ----------------------------------------------------------------------------------
            -------------------------------------------------------------------------------------
            ---------------------------------------------1111</li>
        </ul>
      </el-scrollbar>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
<style lang="scss">
.huangbiao {
  height: 500px;
  width: 500px;
  border: 1px solid green;
  &--header {
    height: 100px;
    border: 1px solid blue;
  }
  &--content {
    height: 300px;
    border: 1px solid red;
    position: relative;
  }
}
.hb-scroll {
  position: absolute;
  top: 0;
  right: 0px;
  left: 0;
  bottom: 17px; // 这是因为 el-scrollbar__wrap 控件使用了 margin-bottom: -17px
  border: 1px solid rebeccapurple;
  overflow-x: hidden;
}
</style>
```











































































