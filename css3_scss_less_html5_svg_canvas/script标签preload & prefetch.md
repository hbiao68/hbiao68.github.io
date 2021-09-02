@[toc]

# 一、文章参考
1. [Link prefetching FAQ](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Link_prefetching_FAQ)
2. [链接类型：preload](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Link_types/preload)
3. [浏览器页面资源加载过程与优化](https://juejin.cn/post/6844903545016156174)


# 二、浏览器(chrome)资源的优先级

## 2.1 浏览器一般的行为方式

1. 浏览器加载的时候是自上而下的，加载和渲染为同步进行

2. 加载不会阻塞下载，解析会阻塞下载

3. js解析的时候会阻塞其他的加载

4. 一般浏览器会在后面解析js文件，因为js中的代码很有可能改变dom树的结构，导致出现重绘和重排的现象

## 2.2 浏览器的加载一般顺序为
先引入一个使用场景来说明问题，图片一般比代码要大，因此加载图片的速度就要比文件慢，如果加载一段代码，图片全部都放在了JS代码引入的前面，之后再引入JS文件，会出现什么情况呢？因为下载图片占用了大量的带宽影响到了JS加载的速度，导致刚进入界面会出现白屏的情况。

现在浏览器变聪明了，有了一定的策略来加载资源，提高用户的体验，顺序如下：
1. 将资源分类
2. 安全策略检查
3. 资源优先级计算
4. 根据优先级下载资源

## 2.3 浏览器加载资源的过程

### 2.3.1 将资源分类

| 类型| 说明  |
|:--|:--|
| kMainResource | 即主资源，html页面文件资源就属于该类型 |
| kImage | 各种图片资源 |
| kCSSStyleSheet | 顾名思义，就是层叠样式表css资源 |
| kScript | 脚本资源，例如js资源 |
| kFont | 字体资源，例如网页中常用的字体集.woff资源 |
| kRaw | 混合类型资源，最常见的ajax请求就属于这类资源 |
| kSVGDocument | SVG可缩放矢量图形文件资源 |
| kXSLStyleSheet | 扩展样式表语言XSLT，是一种转换语言，关于该类型可以查阅w3c XSL来了解 |
| kLinkPrefetch | HTML5页面的预读取资源(Link prefetch)，例如dns-prefetch。下面会有详细介绍 |
| kTextTrack | video的字幕资源，- 即<track>标签 |
| kImportResource | HTML Imports，将一个HTML文件导入到其他HTML文档中，例如<link href="import/post.html" rel="import" />。详细了解请查阅相关文档。 |
| kMedia | 多媒体资源，video or audio都属于该类资源 |
| kManifest | HTML5 应用程序缓存资源 |
| kMock | 预留的测试类型 |

### 2.3.2 安全策略检查

然后根据浏览器相关的安全策略，来决定资源的加载权限。（`是否跨域就是在这个阶段被限制的`）

网页安全政策（Content Security Policy，缩写 CSP）是由浏览器提供的一种白名单制度

第一种，就是通过页面HTTP请求头的Content-Security-Policy字段来限制

第二种，通过<meta>标签来设置。<meta>是以key-value的方式来进行配置的


### 2.3.3 资源优先级计算(分为三类)

1. 对于XHR请求资源：将`同步XHR请求的优先级调整为最高`。

2. 对于图片资源：会根据图片是否在可见视图之内来改变优先级。

`图片资源的默认优先级为Low`。

现代浏览器为了提高用户首屏的体验，在渲染时会计算图片资源是否在首屏可见视图之内，在的话，`会将这部分视口可见图片(Image in viewport)资源的优先级提升为High`

3. 对于脚本资源：浏览器会将根据脚本所处的位置和属性标签分为三类，分别设置优先级

首先，`对于添加了defer/async属性标签的脚本的优先级会全部降为Low`

对于没有添加该属性的脚本，根据该脚本在文档中的位置是在浏览器展示的第一张图片之前还是之后，又可分为两类。在之前的(标记early**)它会被定为High优先级，在之后的(标记late**)会被设置为Medium优先级

### 2.3.4 根据优先级下载资源

什么是关键请求链？
可视区域渲染完毕（首屏），并对于用户来说可用时，必须加载的资源请求队列，就叫做关键请求链
我们可以通过关键请求链，来确定优先加载的资源以及加载顺序，以实现浏览器尽可能快地加载页面

优化关键请求链有很多方法，这里主要介绍两种
第一种：利用Preload和Prefetch。
第二种：利用LocalStorage

# 三、资源预读取prefetch

关键字 prefetch 作为元素 <link>  的属性 rel 的值，是为了提示浏览器，用户未来的浏览有可能需要加载目标资源，所以浏览器有可能通过事先获取和缓存对应资源，优化用户体验。

> 它的作用是告诉浏览器加载下一页面可能会用到的资源;`注意，是下一页面，而不是当前页面`。

## 3.1 什么是链接预取？
链接预取是一种浏览器机制，其利用浏览器空闲时间来下载或预取用户在不久的将来可能访问的文档。网页向浏览器提供一组预取提示，并在浏览器完成当前页面的加载后开始静默地拉取指定的文档并将其存储在缓存中。当用户访问其中一个预取文档时，便可以快速的从浏览器缓存中得到。

## 3.2 语法
浏览器会查找关系类型(rel)为 next 或 prefetch 的 HTML<link>  或 HTTP Link: header

```html
<link rel="prefetch alternate stylesheet" title="Designed for Mozilla" href="mozspecific.css">
<link rel="next" href="2.html">
<link rel="prefetch" href="/images/big.jpeg">
```

## 3.3 能够预取不同宿主的文档吗？
可以。`预取不受同源限制`。限制预取来自同一个服务器并不会对增强浏览器的安全性有所帮助。

## 3.4 a 标签(<a>) 会被预取吗？
不会，只有带有关系类型为 next 或 prefetch 的 <link> 标签会被预拉取。

# 四、资源预加载preload
<link>元素的rel属性的预加载值允许您在HTML的<head>中声明获取请求，指定您的页面将很快需要的资源，您希望`在页面生命周期的早期开始加载这些资源，在浏览器的主要呈现机制开始之前`。 这确保它们更早可用，并且`不太可能阻塞页面的呈现，从而提高性能`。  

表示用户十分有可能需要在当前浏览中加载目标资源，所以浏览器必须预先获取和缓存对应资源

## 4.1 语法介绍

```html
<head>
  <meta charset="utf-8">
  <title>JS and CSS preload example</title>

  <link rel="preload" href="style.css" as="style">
  <link rel="preload" href="main.js" as="script">

  <link rel="stylesheet" href="style.css">
</head>

<body>
  <h1>bouncing balls</h1>
  <canvas></canvas>

  <script src="main.js" defer></script>
</body>
```

link 属性：
* rel="preload"
* href：指定需要被预加载资源的资源路径
* as：指定需要被预加载资源的类型
* type：可以包含该元素所指向资源的MIME类型

```html
<link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
```

## 4.2 预加载的脚本
```js
var preloadLink = document.createElement("link");
preloadLink.href = "myscript.js";
preloadLink.rel = "preload";
preloadLink.as = "script";
document.head.appendChild(preloadLink);
```

## 4.3 加载资源优先级说明

1. preload 使用 “as” 属性加载的资源将会获得与资源 “type” 属性所拥有的相同的优先级。比如说，preload as="style" 将会获得比 as=“script” 更高的优先级

2. 不带 “as” 属性的 preload 的优先级将会等同于异步请求