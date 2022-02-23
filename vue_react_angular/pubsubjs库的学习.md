@[toc]

#  一、	参考
1. [pubsub-js npm地址](https://www.npmjs.com/package/pubsub-js)


# 应用场景描述
在React和Vue开发中，如果遇到同级组件或者跨多级组件的数据传递，可以使用pubsub-js库实现“订阅和发布”的能力，实现“数据跨组件传输”

# 使用说明

## 快速入门(订阅——发布)

```js
// 参考 https://www.npmjs.com/package/pubsub-js
import PubSub from 'pubsub-js'

/**
 * create a function to subscribe to topics
 * @param {*} msg 主题的名字, 案例中的 msg 是 'MY TOPIC'
 * @param {*} data 订阅主题传递的参数
 */
var mySubscriber = function (msg, data) {
  console.log( msg, data );
};

// 订阅一个 'MY TOPIC' 主题, 对应的方法是 mySubscriber
var token = PubSub.subscribe('MY TOPIC', mySubscriber);

// 发布主题 'MY TOPIC', 即 传输的参数是 'hello world!  publish'
// 异步推送
PubSub.publish('MY TOPIC', 'hello world!  publish');

// 同步推送
PubSub.publishSync('MY TOPIC', 'hello world!  publishSync');

test('pubsubjs demo', (done) => {
  // 异步推送
  // PubSub.publish('MY TOPIC', 'hello world!  publish');
  setTimeout(()=>{
    done()
  }, 2000)
})
```

## 发布匹配多个订阅 规则
```js
// 参考 https://www.npmjs.com/package/pubsub-js
import PubSub from 'pubsub-js'

var myFunc1 = function (msg, data) {
  console.log( msg, 'myFunc1', data );
};
var myFunc2 = function (msg, data) {
  console.log( msg, 'myFunc2', data );
};
var myFunc3 = function (msg, data) {
  console.log( msg, 'myFunc3', data );
};


test('pubsubjs demo', (done) => {
  PubSub.subscribe('a', myFunc1);
  PubSub.subscribe('a.b', myFunc2);
  PubSub.subscribe('a.b.c', myFunc3);

  
  // PubSub.publish('a')
  // 会触发一个方法 myFunc1
  // PubSub.publishSync('a', 'publishSync')
  // 会触发三个方法
  // PubSub.publishSync('a.b.c', 'publishSync')

  // 智慧触发 myFunc3 方法
  // PubSub.unsubscribe('a.b');
  // PubSub.publishSync('a.b.c', 'publishSync')

  // 所有的订阅全部取消，一个方法都不会发生
  PubSub.clearAllSubscriptions();
  PubSub.publishSync('a.b.c', 'publishSync')

  setTimeout(()=>{
    done()
  }, 2000)
})
```

## 发布匹配公共订阅和个性化订阅 规则
```js
// 参考 https://www.npmjs.com/package/pubsub-js
import PubSub from "pubsub-js";

test("pubsubjs demo", (done) => {
  // create a subscriber to receive all topics from a hierarchy of topics
  var myToplevelSubscriber = function (msg, data) {
    console.log("top level: ", msg, data);
  };

  // subscribe to all topics in the 'car' hierarchy
  PubSub.subscribe("car", myToplevelSubscriber);

  // create a subscriber to receive only leaf topic from hierarchy op topics
  var mySpecificSubscriber = function (msg, data) {
    console.log("specific: ", msg, data);
  };

  // subscribe only to 'car.drive' topics
  PubSub.subscribe("car.drive", mySpecificSubscriber);

  // Publish some topics
  PubSub.publish("car.purchase", { name: "my new car" }); // 触发 myToplevelSubscriber
  PubSub.publish("car.drive", { speed: "14" }); // 触发 myToplevelSubscriber 和 mySpecificSubscriber
  PubSub.publish("car.sell", { newOwner: "someone else" }); // 触发 myToplevelSubscriber

  setTimeout(() => {
    done();
  }, 2000);
});
```

# 类似应用
参考 [Vue2.x eventBus全局管理事件的“订阅/发布”](https://hbiao68.blog.csdn.net/article/details/111404462)











































































