@[toc]

# 一、文章参考
1. [在浏览器中使用JavaScript module(模块)](https://www.webhek.com/post/ecmascript-modules-in-browsers.html)
2. [script 标签 MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script)


# 二、script 标签的行为

## 2.1 默认行为（无任何修饰）

1. 浏览器会立即加载并执行指定的脚本
2. 会阻塞 HTML 文档解析，并按照它们出现的顺序执行


## 2.2 &lt;script async &gt; 

 -  特性
	* async 仅适用于外链，规定脚本异步执行
	* 下载不会阻塞页面解析
	* 脚本的加载完成后就马上执行，脚本执行时会阻塞 HTML 解析
	* 不会按照出现的顺序执行，`先下载完成哪个就先执行哪个`
	* 执行的时候，有可能页面还没解析完成
	* DOMContentLoaded事件的触发并不受async脚本加载的影响。
 - 脚本下载与 HTML 解析并行，但`一旦脚本加载完成，就会中断 HTML 解析，同时执行脚本`。

## 2.3 &lt;script defer &gt; 
1. 特性
	* defer 仅适用于外链，规定脚本延迟执行
	* 不会阻塞页面解析
	* 会按照出现的`顺序执行`
	* 在 HTML 解析完成后, DOMContentLoaded 之前执行
2. 脚本下载与 HTML 解析并行，`等 HTML 解析完成后每个脚本都会有序执行`


## 2.4 &lt;script type="module"  &gt; 
> 在script标签中写js代码，或者使用src引入js文件时，默认不能使用module形式，即不能使用import导入文件，但是我们可以再script标签上加上type=module属性来改变方式。

### ype="module" 与 async、defer修饰符同时使用

1. &lt;script type="module"> 	为类似于 &lt;script defer> ，但是模块依赖关系也会被下载

2. &lt;script type="module" async>	行为类似于 &lt;script async> ，额外的模块依赖关系也会被下载

### 案例 —— 在HTML中像ES6编码方式编程
1. demo.html
```html
<html>
 <head>
  <script src='main.js' async type="module"></script>
 </head>
 <body>
  <a class="btn">Search</a>
 </body>
</html>
```

2. main.js
```js
import Movie from "./movie.js";

const btnSearch = document.querySelector(".btn");

btnSearch.addEventListener("click", function() {
  let filmeTeste = new Movie(
    "The Lego Movie",
    2014,
    "tt1490017",
    "Movie",
    "https://m.media-amazon.com/images/M/MV5BMTg4MDk1ODExN15BMl5BanBnXkFtZTgwNzIyNjg3MDE@._V1_SX300.jpg"
  );
  console.log(`object test ${filmeTeste}`);
});
```

3. movie.js
```js
class Movie {
  constructor(title, year, imdbID, type, poster) {
    this.title = title;
    this.year = year;
    this.imdbID = imdbID;
    this.type = type;
    this.poster = poster;
  }
}
export default Movie;
```
