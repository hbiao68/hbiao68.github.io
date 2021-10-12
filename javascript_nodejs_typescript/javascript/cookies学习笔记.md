
@[toc]
# 一、参考
1. [cookie 介绍](https://www.runoob.com/js/js-cookies.html)
1. [js-cookie](https://github.com/js-cookie/js-cookie#readme)

# 二、JavaScript cookie

## 2.1 cookie 介绍

### 2.1.1 cookie 的作用
1、在JavaScript中可以利用cookie实现严格的跨页面全局变量
2、cookie是存于用户硬盘上的一个文件，但这个文件通常对应于一个域名，当浏览器再次访问这个域名时，便可以使用这个cookie
3、cookie可以跨越一个域名下的多个网页，但是不能跨越多个域名使用
4、cookie的名或值中不能使用分号、逗号、等号以及空格,解决这个办法使用escape()函数

```js
document.cookie="str="+escape("i = love; you,");
alert(escape("="));
alert(escape(","));
alert(escape(";"));
alert(escape(" "));
```

### 2.1.2 优点
1、保存用户的登陆状态
2、跟踪用户的行为（在网页上选择天气预报的城市）
3、定制页面（自己设定的网页风格——不过也可以通过server来解决）
备注：上面的方法除了第一种情况其余的都可以配合Server来解决

### 2.1.3 缺点
1、cookie牵涉到安全问题，如果禁用那么功能就要被“删除”（可以通过使用默认值来解决）
2、cookie是和浏览器相关的，即不同的浏览器之间保存的cookie是不能相互使用的
3、cookie这个功能可能会被“禁用”


## 2.2 cookie 的 CURD

1、如何建立相应的硬盘文件，即把cookie保存到硬盘中？

```js
var date = new Date();
var expireDays = 10;
date.setTime(date.getTime() + expireDays*24*3600*1000);
//这里需要使用expire这个key，然后就能保存cookie，相当于这是一个关键字
document.cookie = "userId=28;userName=huangbiao;expire="+date.toGMTString();
```

2、删除cookie
```js
function deleteCookie(name){
	var date = new Date();
  //设置日期小于当前的日期
	date.setTime(date.getTime()-10000);
	document.cookie=name+"v;expire="+date.toGMTString();
}
```

3. 添加cookie的值
```js
function addCookie(name,value,expireHours){
	if("" == name || null == name || undefined == name){
		alert("name is null");
		return "";
	}
	if("" == value || null == value || undefined == value){
		alert("value is null");
		return "";
	}
	var cookieString = name+"="escape(value);
	//判断是否设置过期时间
	if(expireHours>0){
		var date = new Date();
		date.setTime(date.getTime() + expireHours*3600*1000);
		cookieString = cookieString+";expire="+date.toGMTString();	
	}
	document.cookie=cookieString;
}
```

4. 得到指定cookie的值
```js
function getCookie(name){
	var strCookie = document.cookie;
	var arrCookie = strCookie.split(";");
	for(var i = 0; i < arrCookie.length; i++){
		var arr = arrCookie[i].split("=");
		if(arr[0] == name)
			return arr[1];	
	}
	return "";	
}
```

## 2.3 常见问题

### 2.3.1 指定可访问的cookie的路径?
例如在www.xxxx.com/html/a.html中所创建的cookie，可以被www.xxxx.com/html/b.html或www.xxx.com/html/some/c.html所访问，但不能被www.xxxx.com/d.html访问

```js
//表示当前cookie仅能在abc目录下使用
document.cookie="userId=32;path=/abc";
//表示在整个网站下可以使用
document.cookie="userId32;path=/";
```

### 2.3.2 指定可访问的cookie的主机名？

例如：www.google.com和gmail.google.com就是两个不同的主机名。默认情况下，一个主机中创建的cookie在另一个主机下是不能被访问的，但可以通过domain参数来实现对其的控制

解决办法：

```js
//domain这个也是特殊属性，指明主机的名称
document.cookie="name=huangbiao;domain=.google.com";
```

# 三、第三方js-cookie

## 3.1 安装
```bash
$ npm i js-cookie
```

## 3.2 引用库

1. ES module 在浏览器中
```html
<script type="module" src="/path/to/js.cookie.mjs"></script>
<script type="module">
import Cookies from '/path/to/js.cookie.mjs'

Cookies.set('foo', 'bar')
</script>
```

2. ES Module 引入
```js
import Cookies from 'js-cookie'

Cookies.set('foo', 'bar')
```

## 3.3 快速入门
```js
Cookies.set('name', 'value', { path: '' })
Cookies.get('name') // => 'value'
Cookies.remove('name', { path: '' })
```

## 3.4 API

1. 写cookie
```js
Cookies.set('name', 'value')
Cookies.set('name', 'value', { expires: 7 })
Cookies.set('name', 'value', { expires: 7, path: '' })
```

2. 读取值
```js
读具体的值
Cookies.get('name') // => 'value'
Cookies.get('nothing') // => undefined
读所有的值
Cookies.get() // => { name: 'value' }
```

3. 删除值
```js
Cookies.set('name', 'value', { path: '' })
Cookies.remove('name') // fail!
Cookies.remove('name', { path: '' }) // removed!
```

4. 命名空间冲突
```js
// Assign the js-cookie api to a different variable and restore the original "window.Cookies"
var Cookies2 = Cookies.noConflict()
Cookies2.set('name', 'value')
```

5. 支持https
```js
secure的值true或false，表示cookie传输是否需要安全协议(https)。  
Cookies.set('name', 'value', { secure: true })
Cookies.get('name') // => 'value'
Cookies.remove('name')
```


