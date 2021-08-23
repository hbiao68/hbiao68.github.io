@[toc]

# 一、文章参考
1. [react-slick Previous and Next methods](https://react-slick.neostack.com/docs/example/previous-next-methods)
2. [react官网——Refs and the DOM](https://react.docschina.org/docs/refs-and-the-dom.html)

# 二、问题描述
今天要写一个轮播图，由于时间有限，只能在网上查找插件，发现了[react-slick](https://react-slick.neostack.com/)，其中有个需求就是通过键盘的keyleft和keyright来切换图片，因此就需要调用插件的函数，因为react是JSX语法，那么我怎么找到`插件的实例`呢？

## 解决思路
在render函数的返回的HTML添加ref属性，来指明当前控件对象

# 三、ref 有什么作用
1. React提供的这个ref属性，`表示为对组件真正实例的引用`，其实就是ReactDOM.render()返回的组件实例；
> ReactDOM.render()渲染组件时返回的是组件实例；渲染dom元素时，返回是具体的dom节点。

2. ref可以挂载到组件上也可以是dom元素上；
> 挂到组件(class声明的组件)上的ref表示对组件实例的引用。不能在函数式组件上使用 ref 属性，因为它们没有实例：挂载到dom元素上时表示具体的dom元素节点。

# 四、react中ref的3种绑定方式

## 方式1: string类型绑定 （过时，不推荐）

1. 定义ref
```js
render() {
  return (
    <Player ref="player">
      <source src={vedioUrl} />
    </Player>
  )
}
```

2. 使用ref找到对应的节点
```js
const { player } = this.refs.player.getState();
```

说明：
* 找到的组件是HTML 则是DOM 实例
* ref指向的是组件，则ref对应的组件实例

## 方式2: react.CreateRef()

1. 定义ref
```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

2. 使用ref找到对应的节点
> 对该节点的引用可以在 ref 的 current 属性中被访问
```js
const node = this.myRef.current;
```

* 找到的组件是HTML 则是DOM 实例
* ref指向的是组件，则ref对应的组件根节点的DOM 节点实例

## 方式3: 函数形式

1. 定义ref
```js
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

说明：
* 这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问
* 如果是组件，则是组件实例
* 如果是HTML，则是DOM节点

## ref 的值根据节点的类型而有所不同
* 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
* 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
* 你不能在函数组件上使用 ref 属性，因为他们没有实例。

# 五、综合案例——解决之前的问题（react-slick）

```js
import BaseComponent from '../../../components/core/BaseComponent';
import styleObj from './LatestVediosComponent.scss'
import Slider from "react-slick"
import { Player } from 'video-react';

class LatestVediosComponent extends BaseComponent {
  // 构造函数
  constructor (props) {
    super(props);
    this.state = {
      // 选中的视频地址
      vedioUrl: '',
      currentSlide: 0,
      // 是否是手机模式，如果显示屏宽度超过720px，就是PC模式，小于720px就是手机模式
      isMobileMode: false,
      serverData: []
    };
    this.next = this.next.bind(this);
    this.previous = this.previous.bind(this);
    this.keyLogic = this.keyLogic.bind(this);
    this.resizeLogic = this.resizeLogic.bind(this);
    this.getRequest = this.getRequest.bind(this);
    this.getSeverData = this.getSeverData.bind(this);
    this.getSeverData()
  }

  componentDidMount () {
    console.log("componentDidMount " + fetch);
    // 添加 resize 绑定事件
    window.addEventListener(
      "keydown",this.keyLogic,false
    );

    // 添加 resize 绑定事件
    window.addEventListener(
      "resize",this.resizeLogic,false
    );
    // 第一次做计算，判断模式
    this.resizeLogic();
  }

  componentWillUnmount () {
    console.log('componentWillUnmount')
    // 删除绑定的resize事件
    window.removeEventListener("keydown",this.keyLogic);
    // 删除绑定的resize事件
    window.removeEventListener("resize",this.resizeLogic);
  }

  getRequest(url) {
    const that = this;
    fetch(url,{
      method:"GET"
    }).then((response) =>{
        if(response.status>=200 && response.status<300){
          return Promise.resolve(response);
        }else{
          return Promise.reject(new Error(response.statusText));
        }
      })
      .then((response) => {
        return response.json();
      })
      .then(function(data){
        let newArr = data.entries.filter(function (currentObj, index) {
          var result = ((currentObj.images[0].url !== undefined) && (currentObj.images[0].url !== '') && (currentObj.images[0].url !== null));
          if (result) {
            return true;
          }else{
            return false;
          }
        })
        that.setState({
          serverData: newArr
        })
      })
      .catch(function(err){
        console.error('response error!');
      });
  }

  // 从服务器获取数据
  getSeverData () {
    var url='https://demo2697834.mockable.io/movies';
    this.getRequest(url)
  }

  next() {
    this.slider.slickNext();
    this.state.currentSlide = this.slider.innerSlider.state.currentSlide;
  }
  previous() {
    this.slider.slickPrev();
    this.state.currentSlide = this.slider.innerSlider.state.currentSlide;
  }

  // 定义逻辑函数
  keyLogic(events){
    var keyStr = events.key;
    if (keyStr === "ArrowRight") {
      this.next()
    }
    if (keyStr === "ArrowLeft") {
      this.previous()
    }
    if (keyStr === "Enter"){
      let {currentSlide, serverData} = this.state;
      let currentMovie = serverData[currentSlide];
      this.selectMovie(currentMovie)
    }
  }

  // 定义逻辑函数
  resizeLogic(){
    console.log(document.documentElement.clientWidth)
    let screenWidth = document.documentElement.clientWidth;
    // 显示屏宽度大于720px则是PC模式
    if (screenWidth > 720) {
      this.setState({
        isMobileMode: false
      });
    }else{ // 显示屏宽度小于于720px则是手机模式
      this.setState({
        isMobileMode: true
      });
    }
  }

  // 存储历史记录
  storeHistory (movieObj) {
    // 用数组来记录查看的记录，有顺序的
    let movieHistory = localStorage.getItem('movie_history');
    // 用JSON对象来判断是否重复，如果
    let movieHistoryJson = localStorage.getItem('movie_history_json');
    if (movieHistory === null || movieHistory === undefined) {
      movieHistory = [];
      movieHistoryJson = {};
    }else{
      movieHistory = JSON.parse(movieHistory);
      movieHistoryJson = JSON.parse(movieHistoryJson);
    }
    // 如果不存在,则添加数据
    if (movieHistoryJson[movieObj.id] === undefined) {
      movieHistory.unshift(movieObj);
      localStorage.setItem('movie_history', JSON.stringify(movieHistory));
      movieHistoryJson[movieObj.id] = movieObj.id;
      localStorage.setItem('movie_history_json', JSON.stringify(movieHistoryJson));
    } else { // 如果存在，删除之前的对象，将现在的对象排在最前面
      for (var i = 0; i < movieHistory.length; i++) {
        var currentObj = movieHistory[i];
        if (currentObj.id == movieObj.id) {
          movieHistory.splice(i, 1);
          break;
        }
      }
      movieHistory.unshift(movieObj);
      localStorage.setItem('movie_history', JSON.stringify(movieHistory));
      movieHistoryJson[movieObj.id] = movieObj.id;
      localStorage.setItem('movie_history_json', JSON.stringify(movieHistoryJson));
    }
  }

  // 选择电影
  selectMovie (movieObj) {
    const that = this;
    // 存储选择电影的历史记录
    this.storeHistory(movieObj);
    let mp4Url = movieObj.contents[0].url;
    // console.log("mp4Url:" + mp4Url);
    // this.setState({
    //   vedioUrl: ''
    // });
    // window.setTimeout(function () {
    //   that.setState({
    //     vedioUrl: mp4Url
    //   });
    // },500)
    let pathObj = {
      // pathname 这个值是不能改的，表示Link需要跳转的界面
      pathname: 'player',
      movieUrl: mp4Url
    };
    this.goPage(pathObj);
  }

  // 获取电影列表的html代码
  getVediosHtmlByData () {
    let {serverData} = this.state;
    let dataArr = serverData;
    var vediosHtml = dataArr.map((item, index) => {
      return (
        <div className={styleObj["vedio-item"]} onClick={this.selectMovie.bind(this,item)} key={index}>
          <div className={styleObj["vedio-item-img"]}>
            <img src={item.images[0].url} />
          </div>
          <div className={styleObj["vedio-item-title"]}>
            {item.title}
          </div>
        </div>
      );
    })
    return vediosHtml;
  }

  // 获取手机模式电影的html代码
  getVediosMobileHtmlByData () {
    let {serverData} = this.state;
    let dataArr = serverData;
    var vediosHtml = dataArr.map((item, index) => {
      return (
        <div className={styleObj["vedio-item"]} onClick={this.selectMovie.bind(this,item)} key={index}>
          <div className={styleObj["vedio-item-img"]}>
            <img src={item.images[0].url} style={{paddingRight: '10px'}}/>
          </div>
          <div className={styleObj["vedio-item-title"]}>
            {item.title}
          </div>
        </div>
      );
    })
    return vediosHtml;
  }

  // 获取播放电影的代码
  getMovieHtml () {
    let {vedioUrl} = this.state;
    if (vedioUrl !== "") {
      return (<Player>
        <source src={vedioUrl} />
      </Player>)
    } else {
      return ""
    }
  }

  render () {
    let {isMobileMode} = this.state;
    let vediosHtml = this.getVediosHtmlByData();
    let vediosMobileHtml = this.getVediosMobileHtmlByData();
    let playerHtml = this.getMovieHtml();
    var settings = {
      dots: false,
      infinite: true,
      speed: 300,
      slidesToShow: 1,
      centerMode: true,
      variableWidth: true
    };
    return (
      <div className={styleObj.LatestVediosComponent}>
        {
          !isMobileMode ? (
            // 横向滚动的效果
            <div>
            <div className={styleObj["vedio-list"]}>
              {/*ref={c => (this.slider = c)} 给插件指向一个句柄 */ }
              <Slider {...settings} ref={c => (this.slider = c)}>
                {vediosHtml}
              </Slider>
            </div>
            <div className={styleObj["vedio-container"]}>
              {playerHtml}
            </div>
          </div>) : (
            // 响应式的界面
            <div>
              <div className={styleObj["vedio-list"]}>
                {vediosMobileHtml}
              </div>
            </div>)
        }
      </div>
    );
  }

}
export default LatestVediosComponent;
```

案例说明
1. `<Slider {...settings} >`添加配置文件的方式
2. `<Slider ref={c => (this.slider = c)}>`获取插件实例的方式
3. `this.slider.innerSlider.state.currentSlide;`获取插件实例的属性



