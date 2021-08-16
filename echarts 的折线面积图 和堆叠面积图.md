
@[toc]

# 一、文章参考
1. [echarts 官方案例](https://echarts.apache.org/examples/zh/index.html)

# 二、问题描述
工作中，同事给我反馈，数据一模一样，但是折线没有重合，出现了bug，感觉最高点像两个数据相加；

![在这里插入图片描述](https://img-blog.csdnimg.cn/881018c82d364539b4232d81eab43e48.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)


自己查找echarts文档之后，发现一个是“面积图” ，一个是“堆叠面积图”，下面关于这两类图好好学习一下，做个笔记


# 三、 面积图 VS 堆叠面积图
## 3.1 面积图：
>  根据面积大小直观的感受数据
[echarts 折线面积图](https://echarts.apache.org/examples/zh/editor.html?c=area-basic)

```js
option = {
    xAxis: {
        type: 'category',
        boundaryGap: false,
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
    },
    yAxis: {
        type: 'value'
    },
    series: [{
        data: [820, 932, 901, 934, 1290, 1330, 1320],
        stack: '总量',
        type: 'line',
        areaStyle: {}
    },{
        data: [420, 932, 901, 934, 1290, 1330, 1320],
        stack: '总量',
        type: 'line',
        areaStyle: {}
    }]
};
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/7e5af6b47aa446ec97b5111b8a7f6550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)

## 3.2 堆叠面积图
> 1.  根据面积大小直观的感受数据
> 2. 也关注纵轴的累计数据（数据一模一样，折线也没有重合，根据面积判断数据大小）

[echarts 堆叠面积图](https://echarts.apache.org/examples/zh/editor.html?c=area-stack)

```js
option = {
    xAxis: {
        type: 'category',
        boundaryGap: false,
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
    },
    yAxis: {
        type: 'value'
    },
    series: [{
        data: [820, 932, 901, 934, 1290, 1330, 1320],
        stack: '总量',
        type: 'line',
        areaStyle: {}
    },{
        data: [420, 932, 901, 934, 1290, 1330, 1320],
        stack: '总量',
        type: 'line',
        areaStyle: {}
    }]
};
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/57381537eabe434d92786e78996471b2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)

## 3.3 echarts 配置字关键字段 —— series.stack、  series.areaStyle
1. 有` series.areaStyle`表示的是面积图
2. 有` series.stack `表示的是堆叠图， 没有则是面积图










