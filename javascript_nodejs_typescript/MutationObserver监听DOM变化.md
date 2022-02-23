@[toc]

# 一、参考
1. [MutationObserver MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver/MutationObserver)
2. [MutationObserver observe() MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver/observe)
3. [MutationObserverInit MDN](https://developer.mozilla.org/zh-CN/docs/conflicting/Web/API/MutationObserver/observe_2f2addbfa1019c23a6255648d6526387)


# 二、快速入门

```js
// 得到要观察的元素
var elementToObserve = document.querySelector("#targetElementId");

// 创建一个叫 `observer` 的新 `MutationObserver` 实例，
// 并将回调函数传给它
var observer = new MutationObserver(function() {
    console.log('callback that runs when observer is triggered');
});

// 在 MutationObserver 实例上调用 `observe` 方法，
// 并将要观察的元素与选项传给此方法
observer.observe(elementToObserve, {subtree: true, childList: true});
```

# 三、MutationObserver相关对象和方法

## 3.1 MutationObserver
* 功能
  MutationObserver接口提供了监视对DOM树所做更改的能力。

* 构造函数 MutationObserver.observe()

* 语法
  var observer = new MutationObserver(callback);

* 参数
  * callback
    一个回调函数，每当被指定的节点或子树以及配置项有Dom变动时会被调用。回调函数拥有两个参数：一个是描述所有被触发改动的 MutationRecord 对象数组，另一个是调用该函数的MutationObserver 对象

## 3.2 MutationObserver.observe()

* 语法
  mutationObserver.observe(target[, options])
* 参数（MutationObserverInit对象）
  * target
        DOM树中的一个要观察变化的DOM Node (可能是一个Element) , 或者是被观察的子节点树的根节点。
  * options 可选
        一个可选的MutationObserverInit 对象，此对象的配置项描述了DOM的哪些变化应该提供给当前观察者的callback。 

## 3.3 MutationObserverInit
	
* 作用
  MutationObserverInit 字典描述了 MutationObserver 的配置。
* 属性
  * attributeFilter 可选
        要监视的特定属性名称的数组。如果未包含此属性，则对所有属性的更改都会触发变动通知。无默认值。
  * attributeOldValue (en-US) 可选
        当监视节点的属性改动时，将此属性设为 true 将记录任何有改动的属性的上一个值。有关观察属性更改和值记录的详细信息，详见Monitoring attribute values in MutationObserver。无默认值。
  * attributes (en-US) 可选
        设为 true 以观察受监视元素的属性值变更。默认值为 false。
  * characterData (en-US) 可选
        设为 true 以监视指定目标节点或子节点树中节点所包含的字符数据的变化。无默认值。
  * characterDataOldValue (en-US) 可选
        设为 true 以在文本在受监视节点上发生更改时记录节点文本的先前值。详情及例子，请查看 Monitoring text content changes in MutationObserver。无默认值。
  * childList 可选
        设为 true 以监视目标节点（如果 subtree 为 true，则包含子孙节点）添加或删除新的子节点。默认值为 false。
  * subtree (en-US) 可选
        设为 true 以将监视范围扩展至目标节点整个节点树中的所有节点。MutationObserverInit 的其他值也会作用于此子树下的所有节点，而不仅仅只作用于目标节点。默认值为 false。 

* 注意事项
			当调用 observe() 方法时，childList，attributes 或者 characterData 三个属性之中，至少有一个必须为 true，否则会抛出 TypeError 异常。


# 四、经典案例

## 4.1 MutationObserver的callback回调函数是异步的，只有在全部DOM操作完成之后才会调用callback。

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
    <div id="target" class="block" name="target">
      target的第一个子节点
      <p>
        <span>target的后代</span>
        <div>
          MutationObserver的callback回调函数是异步的，只有在全部DOM操作完成之后才会调用callback。
        </div>
      </p>
    </div>
  </body>
  <script>
    window.onload = function () {
      // 监听变化的 DOM 节点
      var target = document.getElementById("target");
      var i = 0;
      // 节点变化 触发的回调函数
      var observe = new MutationObserver(function (mutations, observe) {
        i++;
        console.log(mutations) // Array(3) [ MutationRecord, MutationRecord, MutationRecord ]
        mutations.forEach((mutation) => {
          // MutationRecord 
          // {
          //   type:   "childList",
          //   target: div#target.block,
          //   addedNodes: NodeList(  1  ),
          //   removedNodes: NodeList   [],
          //   previousSibling: #text,
          //   nextSibling: null,
          //   attributeName: null,
          //   attributeNamespace: null,
          //   oldValue: null 
          // }
          console.log(mutation)
          switch(mutation.type) {
            case 'childList':
              /* 从树上添加或移除一个或更多的子节点；参见 mutation.addedNodes 与
                mutation.removedNodes */
              break;
            case 'attributes':
              /* mutation.target 中某节点的一个属性值被更改；该属性名称在 mutation.attributeName 中，
                该属性之前的值为 mutation.oldValue */
              break;
          }
        });
        console.log('开始执行', i); //1x
      });
      observe.observe(target, { childList: true });
      target.appendChild(document.createTextNode("1"));
      target.appendChild(document.createTextNode("2"));
      target.appendChild(document.createTextNode("3"));
      console.log(i); //0
    };
  </script>
</html>
```

## 4.2 如何模拟添加节点？

打开开发者控制台，找到需要监听的DOM节点，可以实现节点的增、删、改（属性），就可以出发监听方法

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
    <ul id="ul" class="aaa bbb">
      <li>1</li>
      <li>2</li>
      <li>3</li>
      <li>4</li>
    </ul>
  </body>
  <script>
    window.onload = function () {
      let targetNode = document.querySelector("#ul");
      let config = {
        childList: true,  // 观察目标子节点的变化，是否有添加或者删除
        attributes: true, // 观察属性变动
        attributeOldValue:true,  // 当监视节点的属性改动时，将此属性设为 true 将记录任何有改动的属性的上一个值。有关观察属性更改和值记录的详细信息
        subtree: true,     // 观察后代节点，默认为 false
        characterData: true, // 监视指定目标节点或子节点树中节点所包含的字符数据的变化，无默认值
      };

      
      function callback(mutationList, observer) {
        mutationList.forEach((mutation) => {
          console.log(mutation)
          debugger
          switch (mutation.type) {
            case "childList":
              /* 从树上添加或移除一个或更多的子节点；参见 mutation.addedNodes 与
    mutation.removedNodes */
              if (mutation.removedNodes.length > 0) {
                console.log('删除节点', mutation.removedNodes)
              }
              if (mutation.addedNodes.length > 0) {
                console.log('添加节点', mutation.addedNodes)
              }
              break;
            case "attributes":
              /* mutation.target 中某节点的一个属性值被更改；该属性名称在 mutation.attributeName 中，
    该属性之前的值为 mutation.oldValue */
              console.log('变化的属性是', mutationattributeName)
              break;
          }
        });
      }

      let observer = new MutationObserver(callback);
      observer.observe(targetNode, config);
    };
  </script>
</html>
```
