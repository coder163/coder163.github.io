### 1. JavaScript模块化


#### 1.1. 什么是模块?

 将一个复杂的程序依据一定的规则(规范)封装成几个块(文件), 并进行组合在一起

块的内部数据/实现是私有的, 只是向外部暴露一些接口(方法)与外部其它模块通信

#### 1.2. 为什么使用模块化

因为有了模块，我们就可以更方便地使用别人的代码，想要什么功能，就加载什么模块。但是，这样做有一个前提，那就是大家必须以同样的方式编写模块，否则你有你的写法，我有我的写法，岂不是乱了套！

#### 1.3. 模块化的进化过程

##### 1.3.1. 全局function模式 :

编码: 全局变量/函数

问题: 污染全局命名空间, 容易引起命名冲突/数据不安全


**module1.js**

```js

//数据
let data = 'atguigu.com'

//操作数据的函数
function foo() {
  console.log(`foo() ${data}`)
}
function bar() {
  console.log(`bar() ${data}`)
}
```

**module2.js**

```js
let data2 = 'other data'

function foo() {  //与另一个模块中的函数冲突了
  console.log(`foo() ${data2}`)
}
```
**test1.html**

```html
<script type="text/javascript" src="module1.js"></script>
<script type="text/javascript" src="module2.js"></script>
<script type="text/javascript">

  let data = "修改后的数据"
  foo()
  bar()
</script>
```
> 说明:
> - 全局函数模式: 将不同的功能封装成不同的全局函数
> - 问题: Global被污染了, 很容易引起命名冲突

##### 1.3.2. namespace模式 :

编码: 将数据/行为封装到对象中

解决: 命名冲突(减少了全局变量)

问题: 数据不安全(外部可以直接修改模块内部的数据)


**module1.js**


```js
let myModule = {
  data: 'atguigu.com',
  foo() {
    console.log(`foo() ${this.data}`)
  },
  bar() {
    console.log(`bar() ${this.data}`)
  }
}
```
**module2.js**

```js
let myModule2 = {
  data: 'atguigu.com2222',
  foo() {
    console.log(`foo() ${this.data}`)
  },
  bar() {
    console.log(`bar() ${this.data}`)
  }
}
```
**test2.html**

```html
<script type="text/javascript" src="module2.js"></script>
<script type="text/javascript" src="module22.js"></script>
<script type="text/javascript">
  myModule.foo()
  myModule.bar()

  myModule2.foo()
  myModule2.bar()

  myModule.data = 'other data' //能直接修改模块内部的数据
  myModule.foo()

</script>
```

> 说明
> -  namespace模式: 简单对象封装
> - 作用: 减少了全局变量
> - 问题: 不安全


##### 1.3.3. IIFE模式/增强

IIFE : 立即调用函数表达式--->匿名函数自调用

编码: 将数据和行为封装到一个函数内部, 通过给window添加属性来向外暴露接口

引入依赖: 通过函数形参来引入依赖模块


##### 1.3.4. IIFE模式


**module3.js**

```js
(function (window) {
  //数据
  let data = '测试数据'

  //操作数据的函数
  function foo() { //用于暴露有函数
    console.log(`foo() ${data}`)
  }

  function bar() {//用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
  }
//内部私有的函数
  function otherFun() {
    console.log('otherFun()')
  }

  //暴露行为
  window.myModule = {foo, bar}
})(window)
```
**test3.html**

```html
<script type="text/javascript" src="module3.js"></script>
<script type="text/javascript">
  myModule.foo()
  myModule.bar()
  //myModule.otherFun()  //myModule.otherFun is not a function
  console.log(myModule.data) //undefined 不能访问模块内部数据
  myModule.data = 'xxxx' //不是修改的模块内部的data
  myModule.foo() //没有改变

</script>
```
> 说明:
>    * IIFE模式: 匿名函数自调用(闭包)
>     * IIFE : immediately-invoked function expression(立即调用函数表达式)
>     * 作用: 数据是私有的, 外部只能通过暴露的方法操作
>     * 问题: 如果当前这个模块依赖另一个模块怎么办?

**IIFE模式增强**

引入jquery到项目中

**module4.js**

```js
(function (window, $) {
  //数据
  let data = '测试数据'

  //操作数据的函数
  function foo() { //用于暴露有函数
    console.log(`foo() ${data}`)
    $('body').css('background', 'red')
  }

  function bar() {//用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
  }

  function otherFun() { //内部私有的函数
    console.log('otherFun()')
  }

  //暴露行为
  window.myModule = {foo, bar}
})(window, jQuery)
```
**test4.html**

```html
<script type="text/javascript" src="jquery-1.10.1.js"></script>
<script type="text/javascript" src="module4.js"></script>
<script type="text/javascript">
  myModule.foo()
</script>
```

> 说明
>  * IIFE模式增强 : 引入依赖
>  * 这就是现代模块实现的基石

#### 1.4. 页面加载多个js的问题

页面:

```html
<script type="text/javascript" src="module1.js"></script>
<script type="text/javascript" src="module2.js"></script>
<script type="text/javascript" src="module3.js"></script>
<script type="text/javascript" src="module4.js"></script>
```

> 说明
> - 一个页面需要引入多个js文件

> 问题:
> *  请求过多
> *  依赖模糊
> *  难以维护

> 这些问题可以通过现代模块化编码和项目构建来解决


### 2. 模块化规范

目前JS中的模块化规范由：ES6CommonJS，AMD，CMD


#### 2.1. CommonJS规范

JavaScript官方定义的API只能构建基于浏览器的应用程序

CommonJS API定义很多普通应用程序（主要指非浏览器的应用）使用的API，从而填补了这个空白。它的终极目标是提供一个类似Python，Ruby和Java标准库。这样的话，开发者可以使用CommonJS API编写应用程序，然后这些应用可以运行在不同的JavaScript解释器和不同的主机环境中。


2009年，美国程序员Ryan Dahl创造了node.js项目，将javascript语言用于服务器端编程。这标志"Javascript模块化编程"正式诞生

在浏览器环境下，没有模块也不是特别大的问题，毕竟网页程序的复杂性有限；但是在服务器端，一定要有模块，与操作系统和其他应用程序互动，否则根本没法编程。

NodeJS是CommonJS规范的实现，webpack 也是以CommonJS的形式来书写。

CommonJS定义的模块分为:{

- 模块引用(require)：用来引入外部模块

- 模块定义(exports)：exports对象用于导出当前模块的方法或变量，唯一的导出口

- 模块标识(module)：module对象就代表模块本身


##### 2.1.1. 编码实现

导出和引用的格式

1. 定义暴露模块:

module.exports = value;

exports.xxx = value;

2. 引入模块:

var module = require(模块名或模块路径);


##### 2.1.2. 导出一个对象(推荐)

```js
//导出
module.exports = {
  foo() {
    console.log('moudle1 foo()')
  }
}
```

```js
//引用
let obj = require('./modules/module1')

//调用导出的对象中的函数
obj.foo();
```

##### 2.1.3. 导出一个函数

```js
//导出
module.exports = function () {
  console.log('module2()')
}

```

```js
//引用
let fun = require('./modules/module2')
//调用导出的函数
fun();
```

##### 2.1.4. 导出一个函数列表



```js
//导出
exports.foo = function () {
  console.log('module3 foo()')
}

exports.bar = function () {
  console.log('module3 bar()')
}
```


```js
//引用
let funlist = require('./modules/module3')

funlist.foo();
```


> 注意：其实所谓的导出要么是给exports对象赋值，要么是在exports对象上追加属性，所以一般情况下一个JS文件中会只有一个导出列表



#### 2.2. AMD规范

基于commonJS规范的nodeJS出来以后，服务端的模块概念已经形成，很自然地，大家就想要客户端模块。而且最好两者能够兼容，一个模块不用修改，在服务器和浏览器都可以运行。但是，由于一个重大的局限，使得CommonJS规范不适用于浏览器环境

```js
var math = require('math');

math.add(2, 3);

```
第二行math.add(2, 3)，在第一行require('math')之后运行，因此必须等math.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。**require 是同步的**。

这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。

因此，浏览器端的模块，不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）。这就是AMD规范诞生的背景。


AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：

```js
require([module], callback);
```
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：

```js
require(['math'], function (math) {

　　　　math.add(2, 3);

　　});
```
math.add()与math模块加载不是同步的，浏览器不会发生假死。所以很显然，AMD比较适合浏览器环境。

RequireJS就是AMD规范的实现。



##### 2.2.1. 下载require.js, 并引入

* 官网: http://www.requirejs.cn/

* github : https://github.com/requirejs/requirejs

* 将require.js导入项目: js/libs/require.js

##### 2.2.2. 创建项目结构

  ```
  |-js
    |-libs
      |-require.js
    |-modules
      |-alerter.js
      |-dataService.js
    |-main.js
  |-index.html
  ```

##### 2.2.3. 定义require.js的模块代码

**dataService.js**

```js
define(function () {
  let msg = 'atguigu.com'

  function getMsg() {
    return msg.toUpperCase()
  }

  return {getMsg}
})
```

**alerter.js**

```js
define(['dataService', 'jquery'], function (dataService, $) {
  let name = 'Tom2'

  function showMsg() {
    $('body').css('background', 'gray')
    alert(dataService.getMsg() + ', ' + name)
  }

  return {showMsg}
})
```
##### 2.2.4. 应用主(入口)js: main.js

```js
(function () {
//配置
require.config({
  //基本路径
  baseUrl: "js/",
  //模块标识名与模块路径映射
  paths: {
    "alerter": "modules/alerter",
    "dataService": "modules/dataService",
  }
})
//引入使用模块
require( ['alerter'], function(alerter) {
  alerter.showMsg()
})
})()
```

##### 2.2.5. 页面使用模块:

 ```html
  <script data-main="js/main" src="js/libs/require.js"></script>
 ```



##### 2.2.6. 使用第三方基于require.js的框架(jquery)

    - 将jquery的库文件导入到项目:` js/libs/jquery-1.10.1.js`

    -  在main.js中配置jquery路径

```js
paths: {
          'jquery': 'libs/jquery-1.10.1'
      }
```
- 在alerter.js中使用jquery

```js
define(['dataService', 'jquery'], function (dataService, $) {
    var name = 'xfzhang'
    function showMsg() {
        $('body').css({background : 'red'})
        alert(name + ' '+dataService.getMsg())
    }
    return {showMsg}
})
```


##### 2.2.7. 使用第三方不基于require.js的框架(angular/angular-messages)

将angular.js和angular-messages.js导入项目

* js/libs/angular.js

* js/libs/angular-messages.js

在main.js中配置

```js
(function () {
  require.config({
    //基本路径
    baseUrl: "js/",
    //模块标识名与模块路径映射
    paths: {
      //第三方库
      'jquery' : 'libs/jquery-1.10.1',
      'angular' : 'libs/angular',
      'angular-messages' : 'libs/angular-messages',
      //自定义模块
      "alerter": "modules/alerter",
      "dataService": "modules/dataService"
    },
    /*
     配置不兼容AMD的模块
     exports : 指定导出的模块名
     deps  : 指定所有依赖的模块的数组
     */
    shim: {
      'angular' : {
        exports : 'angular'
      },
      'angular-messages' : {
        exports : 'angular-messages',
        deps : ['angular']
      }
    }
  })
  //引入使用模块
  require( ['alerter', 'angular', 'angular-messages'], function(alerter, angular) {
    alerter.showMsg()
    angular.module('myApp', ['ngMessages'])
    angular.bootstrap(document,["myApp"])
  })
})()
```

页面:

```html
<form name="myForm">
  用户名: <input type="text" name="username" ng-model="username" ng-required="true">
  <div style="color: red;" ng-show="myForm.username.$dirty&&myForm.username.$invalid">用户名是必须的</div>
</form>
```


#### 2.3. ES6规范


##### 2.3.1. 导出函数

```js
//导出
export function foo() {
  console.log('module1 foo()');
}
export let bar = function () {
  console.log('module1 bar()');
}
export const DATA_ARR = [1, 3, 5, 1]
```

```js
//引用
import {foo, bar} from './module1'
import {DATA_ARR} from './module1'
foo()
bar()
console.log(DATA_ARR);

```
##### 2.3.2. 导出函数列表

```js
//导出
let data = 'module2 data'

function fun1() {
  console.log('module2 fun1() ' + data);
}

function fun2() {
  console.log('module2 fun2() ' + data);
}

export {fun1, fun2}
```

```js
//引用
import {fun1, fun2} from './module2'

fun1()
fun2()

```

##### 2.3.3. 默认导出

```js
//导出
export default {
  name: 'Tom',
  setName: function (name) {
    this.name = name
  }
}
```
```js
//引用
import person from './module3'
person.setName('JACK')

```









