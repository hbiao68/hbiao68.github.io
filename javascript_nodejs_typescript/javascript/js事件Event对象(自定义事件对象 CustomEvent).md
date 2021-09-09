
@[toc]
# 一、参考
1. [Event 接口 MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/Event)
2. [createEvent MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createEvent)
3. [ CustomEvent MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/CustomEvent/CustomEvent)
4. [EventTarget.addEventListener MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)

# 二、Event 接口介绍

参考： [Event 接口 MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/Event)

Event 接口表示在 DOM 中出现的事件。

## 2.1 事件分类
1. 一些事件是由`用户触发`的，例如鼠标或键盘事件；

2. 而其他事件常由 API 生成，例如指示动画已经完成运行的事件，视频已被暂停等等。

3. 事件也可以通过`脚本代码触发`，例如对元素调用 HTMLElement.click() 方法，

4. `定义一些自定义事件，再使用 EventTarget.dispatchEvent() 方法将自定义事件派发往指定的目标（target）`。

# 三、 创建过时Event不推荐

## 3.1 document.createEvent

参考：[createEvent MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createEvent)

创建一个指定类型的事件。其返回的对象必须先初始化并可以被传递给 element.dispatchEvent (en-US)。

### 3.1.1 语法`var event = document.createEvent(type);`

**参数**
**event** : 就是被创建的 Event 对象.

**type** : 是一个字符串，表示要创建的事件类型。`事件类型可能包括"UIEvents", "MouseEvents", "MutationEvents", 或者 "HTMLEvents"。`

## 3.1.2 案例
```js
// 创建事件
var event = document.createEvent('Event');

// 定义事件名为'build'.
event.initEvent('build', true, true);

// 监听事件
elem.addEventListener('build', function (e) {
  // e.target matches elem
}, false);

// 触发对象可以是任何元素或其他事件目标
elem.dispatchEvent(event);
```

## 3.2 Event.initEvent()
`已废弃: `该特性已经从 Web 标准中删除，虽然一些浏览器目前仍然支持它，但也许会在未来的某个时间停止支持，请尽量不要使用该特性。

`Event.initEvent() 方法可以用来初始化由Document.createEvent() 创建的 event 实例.`

### 3.2.1 语法 `event.initEvent(type, bubbles, cancelable);`
参数

**type:**  一个 DOMString 类型的字段，定义了事件的类型.

**bubbles:**  `一个 Boolean 值，决定是否事件是否应该向上冒泡. 一旦设置了这个值`，只读属性Event.bubbles也会获取相应的值.

**cancelable:**  `一个 Boolean 值，决定该事件的默认动作是否可以被取消`. 一旦设置了这个值, 只读属性 Event.cancelable 也会获取相应的值.

### 3.2.2 案例
```js
// 创建事件.
var event = document.createEvent('Event');

// 初始化一个点击事件，可以冒泡，无法被取消
event.initEvent('click', true, false);

// 设置事件监听.
elem.addEventListener('click', function (e) {
  // e.target 就是监听事件目标元素
}, false);

// 触发事件监听
elem.dispatchEvent(event);
```


# 四、推荐自定义事件

## 4.1 CustomEvent()
参考：[ CustomEvent MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/CustomEvent/CustomEvent)

构造方法 CustomerEvent() 创建一个新的 CustomEvent 对象。

### 4.1.1 语法`event = new CustomEvent(typeArg, customEventInit);`

**参数**

* typeArg  一个表示 event 名字的字符串

* customEventInit可选

* bubbles 一个布尔值，表示该事件能否冒泡

* cancelable 一个布尔值，表示该事件是否可以取消

### 4.1.2 案例
```js
// add an appropriate event listener
obj.addEventListener("cat", function(e) { process(e.detail) });

// create and dispatch the event
var event = new CustomEvent("cat", {
  detail: {
    hazcheeseburger: true
  }
});
obj.dispatchEvent(event);
```


## 4.2 EventTarget.addEventListener()
参考：[EventTarget.addEventListener MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)

### 4.2.1 语法

```js
target.addEventListener(type, listener, options);
target.addEventListener(type, listener, useCapture);
```

**参数**
**type:**
* 表示监听事件类型的字符串。

**listener:**
* 当所监听的事件类型触发时，会接收到一个事件通知（实现了 Event 接口的对象）对象。
* listener 必须是一个实现了 EventListener 接口的对象，或者是一个函数。

**options 可选**
* capture:  Boolean，表示 listener 会在该类型的事件捕获阶段传播到该 EventTarget 时触发。
* once:  Boolean，表示 listener 在添加之后最多只调用一次。如果是 true， listener 会在其被调用之后自动移除。
* passive: Boolean，设置为true时，表示 listener 永远不会调用 preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。
* useCapture  可选
* Boolean，在DOM树中，注册了listener的元素， 是否要先于它下面的EventTarget，调用该listener。

### 4.2.2 案例

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
    <table id="outside">
      <tr>
        <td id="t1">one</td>
      </tr>
      <tr>
        <td id="t2">two</td>
      </tr>
    </table>
  </body>
  <script>
    // 改变t2的函数
    function modifyText() {
      var t2 = document.getElementById("t2");
      if (t2.firstChild.nodeValue == "three") {
        t2.firstChild.nodeValue = "two";
      } else {
        t2.firstChild.nodeValue = "three";
      }
    }

    // 为table添加事件监听器
    var el = document.getElementById("outside");
    el.addEventListener("click", modifyText, false);
  </script>
</html>

```

## 4.3 EventTarget.removeEventListener

删除使用 EventTarget.addEventListener() 方法添加的事件

### 4.3.1 语法
```js
target.removeEventListener(type, listener[, options]);
target.removeEventListener(type, listener[, useCapture]);
```
参数
**type**
* 一个字符串，表示需要移除的事件类型，如 "click"。

**listener**
* 需要从目标事件移除的 EventListener 函数。

**options 可选**
* 一个指定事件侦听器特征的可选对象。可选项有：
* capture: 一个 Boolean 表示这个类型的事件将会被派遣到已经注册的侦听器，然后再派遣到DOM树中它下面的任何 EventTarget。
* mozSystemGroup: 仅可运行于 XBL 或者 Firefox Chrome，它是一个 Boolean，用于定义是否将侦听器添加到系统组。
* useCapture 可选
* 指定需要移除的 EventListener 函数是否为捕获监听器。如果无此参数，默认值为 false。

## 4.4 EventTarget.dispatchEvent
参考：[EventTarget.dispatchEvent NDN](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/dispatchEvent)

### 4.4.1 语法`cancelled = !target.dispatchEvent(event)`
* **参数**
	event 是要被派发的事件对象。

	target 被用来初始化 事件 和 决定将会触发 目标.

* **返回值**

	当该事件是可取消的(cancelable为true)并且至少一个该事件的 事件处理方法 调用了Event.preventDefault()，则返回值为false；否则返回true。

**注意:**
* 与浏览器原生事件不同，原生事件是由DOM派发的，

* 并通过event loop异步调用事件处理程序，

* **而dispatchEvent()则是同步调用事件处理程序**。

## 4.5 event.preventDefault

参考： [event.preventDefault](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault)

阻止元素发生默认的行为。

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
    <p>Please click on the checkbox control.</p>

    <form>
      <label for="id-checkbox">Checkbox:</label>
      <input type="checkbox" id="id-checkbox" />
    </form>

    <div id="output-box"></div>
  </body>
  <script>
    document.querySelector("#id-checkbox").addEventListener(
      "click",
      function(event) {
        document.getElementById("output-box").innerHTML +=
          "Sorry! <code>preventDefault()</code> won't let you check this!<br>";
        event.preventDefault();
      },
      false
    );
  </script>
</html>
```
