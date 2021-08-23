@[toc]

# 一、参考
1. `强烈推荐` —— [李兵老师的 《浏览器工作原理与实践》](https://time.geekbang.org/column/intro/216)

# 二、 why 事件循环？

先举个例子：一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

1. 最好的解决办法就是使用单线程
2. 要想在线程运行过程中，能接收并执行新的任务，就需要采用事件循环机制

## 2.1 循环任务的演进

### 如果没有事件循环，代码运行完成，程序就终止了
![在这里插入图片描述](https://img-blog.csdnimg.cn/0cd249d08e454630870164ea079e0bd6.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)
代码模拟：

```c
void MainThread(){
  int num1 = 1+2; //任务1
  int num2 = 20/5; //任务2
  int num3 = 7*8; //任务3
  print("最终计算的值为:%d,%d,%d",num1,num2,num3)； //任务4
}
```

### 添加循环机制，保证程序不终止，并且持续运行
![在这里插入图片描述](https://img-blog.csdnimg.cn/523ed3144426493ea73639db63e5ab13.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)

代码模拟：
```c
//GetInput
//等待用户从键盘输入一个数字，并返回该输入的数字
int GetInput(){
  int input_number = 0;
  cout<<"请输入一个数:";
  cin>>input_number;
  return input_number;
}
//主线程(Main Thread)
void MainThread(){
  for(;;){
    int first_num = GetInput()；
    int second_num = GetInput()；
    result_num = first_num + second_num;
    print("最终计算的值为:%d",result_num)；
  }
}
```
> 特点：
> 1. 第一点引入了循环机制
> 2. 第二点是引入了事件

### 引入消息队列，处理其他线程发送过来的任务
![在这里插入图片描述](https://img-blog.csdnimg.cn/1191b85ffe3f40229bca321c6e8d42ba.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)

代码模拟：
```c
TaskQueue task_queue；
void ProcessTask();
void MainThread(){
  for(;;){
    // 阻塞等待任务
    Task task = task_queue.takeTask();
    // 处理任务
    ProcessTask(task);
  }
}
```
> 特点： 引入了消息队列


# 三、如何处理高优先级的任务？
问题引入：以DOM节点的增删改查为例，如果 DOM 发生变化，采用同步通知的方式，会影响当前任务的执行效率；如果采用异步方式，又会影响到监控的实时性。

单线程有如下特点：
1. 单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务

2. 那要是前面的代码不走了，后面就不走了。那的等到猴年马月啊。所以不行，

## 何解决单个任务执行时长过久的问题？
JavaScript 可以通过回调功能来规避这种问题，也就是让要执行的 JavaScript 任务滞后执行。

## 如何权衡效率和实时性？—— 引用微任务的概念
通常我们把消息队列中的任务称为宏任务，每个宏任务中都包含了一个微任务队列，在执行宏任务的过程中，如果 DOM 有变化，那么就会将该变化添加到微任务列表中，这样就不会影响到宏任务的继续执行，因此也就解决了执行效率的问题。
  
等宏任务中的主要功能都直接完成之后，这时候，渲染引擎并不着急去执行下一个宏任务，而是执行当前宏任务中的微任务，因为 DOM 变化的事件都保存在这些微任务队列中，这样也就解决了实时性问题。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1e81c7f413e3459e820d6003b45627dc.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)


# 四、宏任务（代码），微任务（代码）

宏任务：`script，setTimeout，setInterval，setImmediate`

微任务：`Promise`，process.nextTick ，`MutationObserver`

我们只需记住:

1. `当前执行栈执行宏任务完毕前，会立刻先处理所有微任务队列中的事件，然后再去宏任务队列中取出一个事件`

2. 同一次事件循环中，微任务永远在宏任务之前执行。

3. 每个宏任务就开始一次事件循环

![在这里插入图片描述](https://img-blog.csdnimg.cn/0577087d6ef14600ab5b50c5e97991a9.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70#pic_center)

## 微宏任务执行顺序比较?
<font color="#00f" size="6"> process.nextTick() > Promise.then() > setTimeout > setImmediate;

# 五、利用微任务计算宏任务计算的时间

```html
<ul id="container"></ul>
```

```javascript
// 记录任务开始时间
let now = Date.now();
// 插入十万条数据
const total = 100000;
// 获取容器
let ul = document.getElementById('container');
// 将数据插入容器中
for (let i = 0; i < total; i++) {
    let li = document.createElement('li');
    li.innerText = ~~(Math.random() * total)
    ul.appendChild(li);
}

console.log('JS运行时间：',Date.now() - now);
setTimeout(()=>{
  console.log('总运行时间：',Date.now() - now);
},0)
// print: JS运行时间： 187
// print: 总运行时间： 2844
```


# 六、总结
1. `通过异步操作解决了同步操作的性能问题；`

3. `通过微任务解决了实时性的问题。`

