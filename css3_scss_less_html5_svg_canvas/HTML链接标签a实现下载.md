HTML链接标签<a>实现下载

@[toc]

# 一、参考
1. [<A> MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a#attr-download)
1. [URL.createObjectURL()   MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)
1. [URL.revokeObjectURL()  MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/revokeObjectURL)


# 二、问题描述
工作中需要将地图保存为图片，看了canvas 相关的API ，是可以将canvas保存为base64,最终实现地图的上传和下载, 方法如下：

## 2. 1 利用canvas下载图片
```js
// 将图片保存到本地
var saveFile = function () {
  var filename = new Date().toLocaleDateString() + '.jpg'
  var canvas = document.querySelector('#hmapCanvas')
  // 设置保存图片的类型
  var imgdata = canvas.toDataURL()
  var link = document.createElement('a')
  // 对象URL的字符串表示形式将足够小，以解决浏览器的限制  
  link.href = imgdata
  // download 设备点击该链接是下载，下载的名字就是该字符串
  link.download = filename
  // 创建实践对象，是 MouseEvents 的子类
  var event = document.createEvent('MouseEvents')
  // 对event对象初始化，即MouseEvents类的 click 事件
  event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null)
  // 触发 click 事件
  link.dispatchEvent(event)
}
```

## 2.2 思考
看着上面的代码，有如下知识点不熟悉：
1. `link.href` 对应的值不是地址URL地址，是 base64代码？
2. `link.download` 属性的作用？

# 三、 HTML链接标签<a>介绍

参考：[<a> MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a#attr-download)

## 3.1 概念
HTML &lt;a> 元素（或称锚元素）可以通过它的 href 属性创`建通向其他网页、文件、同一页面内的位置、电子邮件地址或任何其他 URL 的超链接`

&lt;a> 中的内容应该应该指明链接的意图。

如果存在 href 属性，当 &lt;a> 元素聚焦时按下回车键就会激活它。

## 3.2 案例
1. 访问一个网页
```html
<a href="https://example.com">Website</a>
```

2. 发送邮件
```html
<a href="mailto:m.bluth@example.com">Email</a>
```

3. 拨打电话
```html
<a href="tel:+123456789">Phone</a>
```

注意：`非标准的HTML连接要，区分 IOS（苹果） 还是 其他平台，效果有差异`

## 3.3 属性说明

**download**

* `此属性指示浏览器下载 URL 而不是导航到它`，因此将提示用户将其保存为本地文件。

* `如果属性有一个值，那么此值将在下载保存过程中作为预填充的文件名`（如果用户需要，仍然可以更改文件名）。

**href**
> 包含超链接指向的 URL 或 URL 片段。

* URL 片段是哈希标记（#）前面的名称，`哈希标记指定当前文档中的内部目标位置（HTML 元素的 ID）。`

*   `可以使用 href="#top" 或者 href="#" 链接返回到页面顶部`。这种行为是 HTML5 的特性。

* URL 不限于基于 Web（HTTP）的文档，也可以使用浏览器支持的任何协议

*   在大多数浏览器中正常工作的file:、ftp:和mailto：
 
**target**
* _self: `当前页面加载`，即当前的响应到同一HTML 4 frame（或HTML5浏览上下文）。此值是默认的，如果没有指定属性的话。

* _blank: `新窗口打开`，即到一个新的未命名的HTML4窗口或HTML5浏览器上下文

* _parent: 加载响应到当前框架的HTML4父框架或当前的HTML5浏览上下文的父浏览上下文。`如果没有parent框架或者浏览上下文，此选项的行为方式与 _self 相同`。

* _top: IHTML4中：加载的响应成完整的，原来的窗口，取消所有其它frame。 HTML5中：加载响应进入顶层浏览上下文（即，浏览上下文，它是当前的一个的祖先，并且没有parent）。`如果没有parent框架或者浏览上下文，此选项的行为方式相同_self`

# 四、 下载
可以使用 blob: URL 和 data: URL ，以方便用户下载使用 JavaScript 生成的内容

## 4.1 URL.createObjectURL()

### 4.1.1 概念

URL.createObjectURL() 静态方法会创建一个 DOMString，其中包含一个表示参数中给出的对象的URL。

这个 URL 的生命周期和创建它的窗口中的 document 绑定。

**`这个新的URL 对象表示指定的 File 对象或 Blob 对象。`**

### 4.1.2 语法 `objectURL = URL.createObjectURL(object);`

参数
object: 用于创建 URL 的 File 对象、Blob 对象或者 MediaSource 对象。​

返回值
一个DOMString包含了一个对象URL，该URL可用于指定源 object的内容。

### 4.1.3 内存管理
`在每次调用 createObjectURL() 方法时，都会创建一个新的 URL 对象，即使你已经用相同的对象作为参数创建过。当不再需要这些 URL 对象时，每个对象必须通过调用 URL.revokeObjectURL() 方法来释放。`

浏览器在 document 卸载的时候，会自动释放它们，但是为了获得最佳性能和内存使用状况，你应该在安全的时机主动释放掉它们。

### 4.1.4 案例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <input
      type="file"
      id="fileElem"
      multiple
      accept="image/*"
      style="display:none"
      onchange="handleFiles(this.files)"
    />
    <a href="#" id="fileSelect">Select some files</a>
    <div id="fileList">
      <p>No files selected!</p>
    </div>
  </body>

  <script>
    window.URL = window.URL || window.webkitURL;

    var fileSelect = document.getElementById("fileSelect"),
      fileElem = document.getElementById("fileElem"),
      fileList = document.getElementById("fileList");

    fileSelect.addEventListener(
      "click",
      function(e) {
        if (fileElem) {
          fileElem.click();
        }
        e.preventDefault(); // prevent navigation to "#"
      },
      false
    );

    function handleFiles(files) {
      if (!files.length) {
        fileList.innerHTML = "<p>No files selected!</p>";
      } else {
        fileList.innerHTML = "";
        var list = document.createElement("ul");
        fileList.appendChild(list);
        for (var i = 0; i < files.length; i++) {
          var li = document.createElement("li");
          list.appendChild(li);
          var img = document.createElement("img");
          img.src = window.URL.createObjectURL(files[i]);
          console.log(img.src);
          debugger;
          img.height = 60;
          img.onload = function() {
            window.URL.revokeObjectURL(this.src);
          };
          li.appendChild(img);
          var info = document.createElement("span");
          info.innerHTML = files[i].name + ": " + files[i].size + " bytes";
          li.appendChild(info);
        }
      }
    }
  </script>
</html>
```

* 创建一个无序列表 (&lt;ul>) 元素。
* 通过调用列表的Node.appendChild()方法来将新的列表元素插入到 &lt;div>块。
* 遍历文件集合 FileList（即files）中的每个 File：
  * 创建一个新的列表项（&lt;li>）元素并插入到列表中。
  * 创建一个新的图片（&lt;img>）元素。
  * `设置图片的源为一个新的指代文件的对象URL，使用window.URL.createObjectURL() (en-US)来创建blob URL。`
  * 设置图片的高度为60像素。
  * 设置图片的load事件处理器来释放对象URL，当图片加载完成之后对象URL就不再需要了。`这个可以通过调用window.URL.revokeObjectURL() (en-US)方法并且传递 img.src中的对象URL字符串来实现。`
  * 将新的列表项添加到列表中。

## 4.2 URL.revokeObjectURL() 

### 4.2.1 概念
URL.revokeObjectURL() 静态方法用来释放一个之前已经存在的、通过调用 URL.createObjectURL() 创建的 URL 对象。

### 4.2.1 语法`window.URL.revokeObjectURL(objectURL);`

参数
objectURL:一个 DOMString，表示通过调用 URL.createObjectURL() 方法产生的 URL 对象。

返回值
undefined

## 4.3 Data URLs

参考 ： [Data URLs MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Data_URIs)

### 4.3.1 语法`data:[<mediatype>][;base64],<data>`
1. 前缀(data:)

2. 指示数据类型的MIME类型

3. mediatype 是个 MIME 类型的字符串，例如 "image/jpeg" 表示 JPEG 图像文件。如果被省略，则默认值为 text/plain;charset=US-ASCII(如果非文本则为可选的base64标记)

4. 数据本身

### 4.3.2 案例

1. 简单的 text/plain 类型数据
> data:,Hello%2C%20World!

2. 上一条示例的 base64 编码版本
> data:text/plain;base64,SGVsbG8sIFdvcmxkIQ%3D%3D

3. 一个HTML文档源代码 <h1>Hello, World</h1>
> data:text/html,%3Ch1%3EHello%2C%20World!%3C%2Fh1%3E

4. 一个会执行 JavaScript alert 的 HTML 文档。注意 script 标签必须封闭。
> data:text/html,<script>alert('hi');</script>

