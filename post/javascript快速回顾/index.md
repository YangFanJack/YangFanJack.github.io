
再次回顾前端三件套

1. **HTML（结构）**：超文本标记语言，决定网页的结构和内容
2. **CSS（表现）**：层叠样式表，设定网页的表现样式
3. **JavaScript（行为）**：一种弱类型的脚本语言，其源代码不需要经过编译，由浏览器解释运行，用于控制网页的行为。

**历史**

1. Native原生JS开发：按照【ECMAScript】标准的开发方式，特点是所有浏览器都支持。（目前的主流版本是ES6，但是ES5才是所有浏览器都支持，所以需要用webpack把ES6打包为ES5）
2. TypeScript微软标准：微软开发的语言，是JavaScript的超集。除了具备ES的特性之外还有很多不在范围内的新特性，会导致很多浏览器不能直接支持Typescript语法，需要编译后才能被浏览器正确执行。

**Javascript流行库 & 框架**

1. jQuery：简化原生JavaScript操作的JS库
2. Angular：Goole收购的前端框架
3. React：Facebook开发的高性能JS前端框架
4. Vue：渐进式JavaScript框架，特点是综合了Angular(模块化)和React(虚拟DOM)的优点
5. Axios：前端通信框架

**UI框架：**

1. Ant-Design：阿里巴巴
2. ElementUI，iview，ice：饿了么
3. Boostrap：Twitter
4. AmazeUI：妹子UI

**JavaScript构建工具**

1. Babel：Typescript编译工具
2. Webpack：模块打包器

#### 世界上最流行的脚本语言——Javascript

ECMAScript是JavaScript的一种规范，目前已经到ES6了。但是大部分浏览器只支持到ES5

**作为一名后端程序员，必须要精通Javascript！**

## 快速入门

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <!--第二种外部引入方式-->
        <script src="js/a.js"></script>
        <!--第一种方式-->
        <script type="text/javascript">
    		alert("hello,world!");
    	</script>
    </head>
    <body>
        
    </body>
</html>
```

## 基本语法

**基本上按照java语法来写JavaScript一定没有问题：**

```javascript
//定义变量（可带$和_）
var num = 1;
//条件控制
if(2>1){
    alert("true");
}
//JavaScript严格大小写
console.log(score);
```

#### var 和 let的区别

JavaScript中的变量大致分为**局部变量**和**全局变量**

大体上：函数外声明的就是全局变量，函数内声明的就是局部变量

变量是需要用var关键字声明的。但是javascript中也可以隐式的使用变量，就是不用声明，直接使用。但是千万注意，**javascript把隐式声明的变量总是当成全局变量来使用的**。

let和var的区别体现在作用域上：**var的作用域**是函数作用域，**let作用域**则被规定为块作用域，块作用域要比函数作用域小一些，但是如果两者既没在函数中，也没在块作用域中定义，那么两者都属于全局作用域。

举个栗子：

```html
<!--有五个按钮-->
<div>
    <button>按钮1</button>
    <button>按钮2</button>
    <button>按钮3</button>
    <button>按钮4</button>
    <button>按钮5</button>
</div>
```

```javascript
var btns=document.querySelectorAll('button');
for(var i = 0;i<btns.length;i++){
     btns[i].οnclick=function(){
         alert('点击了第'+i+'个按钮');     
     }
}
//点击按钮后弹出的都是第4个，原因是var定义变量i作用域造成的。
//通过var关键字声明的变量只有函数作用域，没有块作用域，在块{ }内声明的变量可以从块之外进行访问。
//通过let关键字声明的变量拥有块作用域，在块{ }内声明的变量无法从块外访问
let btns=document.querySelectorAll('button');
for(let i = 0;i<btns.length;i++){
     btns[i].οnclick=function(){
         alert('点击了第'+i+'个按钮');     
     }
} 

//JavaScript中，函数查找变量从自身函数开始，从 '内' 向 '外' 查找，假设外部存在这个同名的函数变量，则内部函数会屏蔽外部函数的变量。（就近原则）
```

#### 数据类型

* `number`：js不区分小数和整数

  ```javascript
  123//整数
  123.1//浮点数
  1.23e3//科学计数法
  -99//负数
  NaN//not a number
  Infinity//无限大
  ```

* `string`：

  ```javascript
  'abc'
  "abc"
  ```

* `boolean`：

  ```javascript
  true
  false
  &&  ||  !  =  ==  ===(类型一样且值一样)
  NaN和所有数值都不相对，包括自己，只能isNaN()来判断
  ```

* `null`：空

* `undefined`：未定义

* `object`：对象

  * `Function`：

  * `普通object`：

    ```javascript
    var person={
        name: "qinjiang",
        age: 3,
        tags: ['js','java','web']
    }
    ```

  * `Array`：数组中可以有各个类型的值

    ```javascript
    var arr = {1,2,3,4,5,'hello',true}
    var arr = new Array(1,2,3,4,5,'hello',true);
    //数组下标越界会undefined
    ```

  * `Date`：

  * ...

#### 严格检查模式

```javascript
//预防JavaScript的随意性导致的各种问题，必须写在第一行
'use strict'
let i = 1;//局部变量建议使用let
```

## 数据类型

### 字符串

* 使用单引号或者双引号包裹

* 注意转义字符 \

  ```javascript
  \'
  \n
  \t
  \u字符 //Unicode字符
  \x字符 //AscII字符
  ```

* 多行字符串编写

  ```javascript
  var msg = `fasdfdsafsad
  fasdfdsa
  fasdfsda`;//单反引号
  ```

* 模板字符串

  ```javascript
  let name = "jackyang";
  let age = 3;
  let msg = `你好，${name}`;
  ```

* 字符串长度

  ```javascript
  var student = "student";
  console.log(studnet.length);
  console.log(studnet[0]);
  //字符串不可变，下标只能访问
  ```

* 其他函数

  ```javascript
  student.toUpperCase();
  student.toLowerCase();
  student.indexOf('t');
  studnet.subString(1,3);//[)
  ```

### 数组

* 长度

  ```javascript
  var arr = [1,2,3,4,'hello'];
  arr.length;
  /*数组长度可变，通过给length赋值
  赋值过大，多余的元素是undefined
  赋值过小，元素会丢失
  */
  arr.length = 10;
  ```

* 索引

  ```javascript
  //获得下标索引
  arr.indexOf('hello');
  ```

* 切割 & 压进 & 弹出

  ```javascript
  slice();//截取Array的一部分，返回一个新数组
  push();//压入尾部
  pop();//从尾部弹出
  unshift();//从头部压入
  shift();//从头部弹出
  ```

* 排序 & 反转

  ```javascript
  //自身会改变
  arr.sort();
  arr.reverse();
  ```

* 拼接

  ```javascript
  var arr = ['C','B','A'];
  arr.concat([3,4,5]);//没有修改数组，指挥返回一个新的数组['C','B','A',3,4,5]
  
  arr.join('-');//把数组中的元素通过指定分隔符链接在一起"C-B-A"
  ```

* 多维数组

  ```javascript
  var arr = [[1,2],[3,4],['a','b']];
  console.log(arr[1][1]);
  ```

### 对象

* {...}表示一个对象，内容由若干个键值对组成。JavaScript中所有键都是字符串，值是任意对象

  ```javascript
  var person = {
      name: "jackyang",
      age: 21,
      email: "yangfanjack1024@qq.com",
      score: 100
  }
  ```

* 对象赋值

  ```javascript
  person.name="samsmith";
  ```

* 使用一个不存在的对象属性，不会报错：undefined

  ```javascript
  person.haer;
  ```

* 动态的删减 & 添加属性

  ```javascript
  delete person.name;//delete
  person.haha = "heihei";//add
  ```

* 判断属性值是否在对象中

  ```javascript
  'email' in person;//true
  'toString' in person;//继承下来的
  //判断一个属性是否是这个对象自身独有的
  person.hasOwnProperty('toString');
  ```

### 流程控制

* 判断

```javascript
var age = 3;
if(age<3){
   alert("小于3");
}
else if(age<5){
   alert("大于等于三小于5");
}
else{
   alert("大于等于5");
}
```

* 循环

```javascript
var age = 3;
while(age<100){
    age = age +1;
    console.log(age);
}

for(let i=0;i<100;i++){
    console(i);
}

do{
    age=age+1;
    console.log(age);
}while(age<100);

var age = [1,2,3,4,5,6,7];
age.forEach(function(value){
    console.log(value);
})

for(const num in age){//num是下标
    console.log(age[num]);
}
 
for (const num of age) {//num是每个值
  console.log(num);
}

//遍历map和set只能使用of，不能使用in
```

### Map & Set集合

> ES6的新特性

```javascript
//Map
var map = new Map([['tom',100],['jack',99],['haha',80]]);
var name = map.get('tom');//获取
console.log(name);//100

map.set('jack',22);//新增 & 修改
map.delete('jack');//删除

//Set:无序不重复集合
var set = new Set([1,2,3,1,1,1]);
set.add(4);//新增
set.delete(2);//删除
console.log(set.has(3));//判断是否包含


//ES6新增了Sysbol.iterator，可用来遍历String,Array,Map,Set

// Array
var arr = ['a', 'b', 'c', 'd', 'e'];
var eArr = arr[Symbol.iterator]();
console.log(eArr.next().value); // a
console.log(eArr.next().value); // b
console.log(eArr.next().value); // c
console.log(eArr.next().value); // d
console.log(eArr.next().value); // e

// Map
const map1 = new Map();

map1.set('0', 'foo');
map1.set(1, 'bar');

const iterator1 = map1[Symbol.iterator]();

for (let item of iterator1) {
  console.log(item);
}
```

## 函数 & 面向对象

### 函数定义及变量作用域

方法：对象包含（属性，方法），对象中的函数就是方法

> 定义方式1

```javascript
//绝对值函数
function abs(x){
    if(x>=0){
       	return x;
    }
    else{
        return -x;
    }
}
console.log(abs(-10));
//如果没有执行return，函数执行完也会返回一个undefined结果
```

> 定义方式2

```javascript
var abs = function(x){
    if(x>=0){
       	return x;
    }
    else{
        return -x;
    }
}
```

> 调用函数

```javascript
abs(-10);
abs(10);
//JavaScript函数参数可以少传也可以多传，也不会报错

//arguments是函数内一个隐含参数，代表传进来的所有参数，是一个数组
//rest是ES6新特性，获取除了已经定义的参数之外的所有参数,只能写在最后面，前面加...
function aaa(a,b,...rest){
    console.log(a);
    console.log(b);
    console.log(arguments);
    console.log(rest);
}
```

> 提升变量作用域

```javascript
function qj(){
    var x = "x" + y;
    console.log(x);
    var y = 'y';
}
//结果是undefined
//js引擎会自动提升y的声明，但是不会提升y的赋值，上述等价于
function aj(){
    var y;
    
    var x = "x" + y;
    console.log(x);
    y = 'y';
}
//养成规范：所有变量的定义都放在在最上面
```

```javascript
window//全局对象
//默认所有全局变量和函数都自动绑定再window对象下
//JavaScript一切皆对象，也就是所有全局对象都在window对象下

var my_alert = window.alert;
my_alert('hello');//弹
window.alert = function(){
    
}
alert('hello');//不弹
```

> 规范

由于所有全局变量都会绑定在window上，所以不同js文件如果全局变量同名就很麻烦。所以要通过一些规范避免冲突：

```javascript
//唯一全局变量
var jackyang = {};
//定义全局变量
jackyang.name = "jackyang";
jackyang.add = function(a,b){
    return a+b;
}
```

把自己的代码全部放进自定义的唯一空间名字中，从而避免全局命名冲突的问题，jQuery就是这样的

>  let 和 var 的区别见上面

> 常量 const（ES6新特性）

常量不能修改，只能一次赋值

### 方法

```javascript
//方法就是把函数放在对象中，其实函数实际也可以看作window对象的方法
function hi(){
    alert(this.name+',hi!');
}
var person = {
    name: "jackyang",
    birth: 2020,
    email: "yangfanjack1024@qq.com",
    score: 100,
    getAge: function(){
        var now = new Date().getFullYear();
        return now-this.birth;
    },
    sayHi: hi
}

person.sayHi();
person.getAge();
window.hi();//hi是全局函数，只能用window调用
```

this默认指向想用这个方法的那个对象

> apply

每个方法都有一个apply方法，可以改变this的指向

```javascript
hi.apply(person,[]);//this指向person对象，参数为空
person.hi();//这样就能通过person直接调用hi了
```

### 面向对象编程

在JavaScript中，类 & 对象的概念和传统面向对象语言，Java,C#有区别

> 原型

```javascript
var Student = {
    name: "Sam Smith",
    age: 26,
    run: function(){
        console.log(this.name + "run...");
    }
};

var xiaoming = {
    name: "xiaoming"
};

//原型对象Student可以理解为父类，一个子类的模板原型
xiaoming.__proto__ = Student;
xiaoming.run();
```

> class继承（ES6新特性）

```javascript
//以前通过构造函数创建对象
/*构造函数执行流程：
	1. 立刻创建一个新的对象
	2. 将新建的对象设置为函数中的this
	3. 逐行执行函数中的代码
	4. 将新建的对象作为返回值返回
*/
function Student(a){//构造函数和普通函数的区别主要是首字母大写 & 调用方法
    name = "window";//往全局的window中添加
    this.name = a;//往对象中添加
}
var xiaoming = new Student("xiaoming");
var xiaohong = new Student("xiaohong");
console.log(xiaoming instanceof Student);//使用instanceof判断对象是否是某个构造函数的实例
//给Student新增一个方法（可以理解为父类的公共方法）
Student.prototype.hello = function(){
    alert)('hello');
}
//所有对象都是object的后代


//ES6中class定义一个类(class继承,本质还是原型链)
class Student{
    constructor(name){
        this.name = name;
    }
    hello(){
        alert('hello');
    }
}
var xiaoming = new Student("xiaoming");
var xiaohong = new Student("xiaohong");

class XiaoStudent extends Student{
    constructor(name,grade){
        super(name);
        this.grade = grade;
    }
    myGrade(){
        alert("I am a xiao student.");
    }
}
```

### 闭包

> 难点

要理解闭包，首先要理解Javascript的变量作用域。

变量的作用域有两种：全局变量和局部变量。函数内部可以直接读取全局变量。函数外部无法读取函数内的局部变量。

我们有时候需要得到函数内的局部变量。正常情况下是办不到的，只有通过变通方法才能实现。那就是**在函数的内部，再定义一个函数**。

```javascript
function f1(){
 	var n=999;
 	function f2(){
		alert(n); // 999
	}
}
```

既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，就可以在f1外部读取它的内部变量了

```javascript
function f1(){
	var n=999;
	function f2(){
		alert(n);
	}
	return f2;
}
 
var result=f1();
 
result(); // 999
```

闭包就是能够读取其他函数内部变量的函数

在本质上，闭包就是**将函数内部和函数外部连接起来的一座桥梁**

闭包的两中常见应用：

1. 将函数作为另一个函数的返回值

   ```javascript
   function fn1(){
       var a = 2;
       function fn2(){
           a++;
           console.log(a);
   	}
       function fn3(){
           a--;
           console.log(a);
       }
       return fn3;
   }
   var f = fn1();
   f();//3
   f();//4
   //如果这个没有闭包的话，调完fn1()后a就消失了，再调用f()就会报错找不到a
   ```

2. 将函数作为实参传递给另一个函数调用

   ```javascript
   function showDelay(msg, time){
       setTimeout(function(){
           alert(msg)
       },time)
   }
   showDelay('trigger',2000);
   ```

> **闭包的作用：**

1. 使函数内部的变量在函数执行完后，仍然存活在内存中（延长了局部变量的生命周期）
2. 让函数外部可以操作（读写）到函数内部的数据

#### 闭包核心

函数执行完后，函数内部声明的局部变量一般情况下会释放的。但是存在闭包关系中的变量（如上面fn1函数的a变量）才**可能**存在。为什么是可能呢？因为必须要还要有引用指向fn1函数的返回值，如上的f变量指向的fn1的返回值fn3，fn3中有a，所以a才存在闭包关系，此时才能保证fn1执行完a变量并不会释放。

### 箭头函数

> ES6新特性

箭头函数也叫lambda表达式，其主要意图是定义轻量级的内联回调函数

```javascript
var arr = ["wei","ze","yang"];
arr.map(item=>"Mr."+item);   // ["Mr.wei", "Mr.ze", "Mr.yang"]

//等价于
var arr = ["wei","ze","yang"];
arr.map(function(item){
    return "Mr."+item;
});
```

### 原型链继承

> 难点

什么是原型？

```javascript
//我们所创建的每个函数，解析器都会向函数中添加一个属性prototype
//这个属性对应一个原型对象
//如果这个函数作为普通函数调用时，prototype没有任何作用
//当函数作为构造函数调用时，它所创建的对象中都会有一个隐含的属性，指向该构造函数的原型对象
function Student(a){
    name = "window";
    this.name = a;
}
var stu = new Student();
var stu2 = new Student();
console.log(stu.__proto__ == Student.prototype);//true
console.log(stu2.__proto__ == Student.prototype);//true

//原型对象相当于是一个公共区域，所有同一个类的实例都可以访问这个原型对象

//可以把对象中共有的内容放到原型对象中
```

以后我们创建构造函数时，可以将这些对象共有的属性和方法统一添加到构造函数的原型对象中，这样就不用分别为每一个对象添加，也不会影响到全局作用域，就可以使每个对象都具有这些属性和方法了

> 什么是原型链

原型对象也是对象，所以他也有原型

**当我们访问对象的一个属性或者方法时，会先在对象自身中寻找，如果有则直接使用，如果没有则会去原型对象中寻找，如果找到就直接使用，如果没有就去原型对象的原型对象中寻找，直到找到Object对象的原型，Object对象的原型没有原型**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223833.png" style="zoom: 80%;" />

## 常用对象

> JavaScript标准对象：number  string  boolean  NaN  object  function  undefined  Infinity

### Date

```javascript
var now = new Date();
now.getFullYear();//年
now.getMonth();//月0到11
now.getDate();//日
now.getDay();//星期几
now.getHours();//时
now.getMinutes();//分
now.getSeconds();//秒
now.getTime();//时间戳（全世界统一：1970.01.01 00:00:00开始到现在的毫秒数）
console.log(new Date(1610499819016));//时间戳转标准时间格式

//几种时间的格式转换
now.toLocalString();
now.toGMTString();
now.toISOString
```

### JSON

> JSON是啥？

* JSON（JavaScript Object Notation，JS对象简谱）是一种轻量级的数据交换格式
* 简介和清晰的层次结构使得JSON成为理想的数据交换语言
* 易于阅读和编写，易于机器解析和生成，能有效提升网络传输效率

JavaScript中一切皆对象，任何js类型都可以用JSON表示

* 对象都用`{}`
* 数组都用`[]`
* 键值对用`key:value`

```javascript
var user = {
    name: "Mariah Carey",
    age: 50,
    sex: 'male'
}

//JavaScript对象转json字符串
var jsonUser = JSON.stringify(user);
//json字符串转JavaScript对象
//里面双引号外面单引号，里面双引号外面单引号
JSON.parse('{"name":"Mariah Carey","age":50,"sex":"male"}');
```

```json
{
    "name":"Mariah Carey",
    "age":50,
    "sex":"male"
}
```

> JavaScript对象和JSON字符串的区别

* 首先，一个是对象，一个是字符串
* json字符串的key需要引号，JavaScript不需要

> 前后端分离传数据一般是配合ajax结合

* 原生js写法，xhr异步请求
* jQuery封装好的方法`$("#name").ajax()`
* axios请求

## 操作BOM元素

> JavaScript 由三部分构成，ECMAScript，DOM（Document对象模型）和BOM（浏览器对象模型）

> JavaScript的诞生就是为了能让它能再浏览器中运行

### window

```javascript
//window代表浏览器窗口
//window对象是BOM中所有对象的核心，除了是BOM中所有对象的父对象外，还包含一些窗口控制函数。
window.alert('hello');

//所有BOM对象的父对象
window.navigator;
window.screen;
window.location;
window.document;
window.history;

//通过调整浏览器窗口大小来改变这些浏览器相关值的大小
window.innerHeight;
window.innerWidth;
window.outerHeight;
window.outerWidth;
```

### navigator

```javascript
//封装了浏览器的信息
navigator.appName;
navigator.appversion;
navigator.userAgent;
navigator.platform;
//一般情况不会使用navigator对象，因为会人为篡改，不能把这些数据作为判断浏览器的标准
//不建议使用
```

### screen

```javascript
//代表屏幕尺寸
screen.width;
1920px
screen.height;
1080px
```

### location

```javascript
//代表当前页面的URL信息
host: "www.bilibili.com"
href: "https://www.bilibili.com/video/BV1JJ41177di?p=19"
origin: "https://www.bilibili.com"
pathname: "/video/BV1JJ41177di"
protocol: "https:"
reload: ƒ reload()//刷新网页
assign: ƒ assign()//设置新的地址

//重定向地址有三种方法：
location.assign("https://jack1024.link");
location.href="https://jack1024.link";
location.replace("https://jack1024.link");
//replace和上面两种方法的区别是，replace的浏览历史会被清空了（href与assign方法会产生历史记录），所以建议使用repalce，这样就不能点击浏览器左上角的左右按钮回退了，更加安全。
```

### document

```javascript
//document代表当前页面的HTML DOM文档树，具体的DOM在下面DOM中介绍
document;//获取文档树
document.cookie;//获取网页cookie
```

### history

```javascript
//代表浏览器的历史记录
history.back();
history.forward();
//不建议使用
```

## 操作DOM元素

> 浏览器网页就是一个DOM属性结构

### 获取DOM

```html
<div id="div1">
	<h1>标题1</h1>
	<p id="p1">段落1</p>
	<p class="p2">段落2</p>
</div>

<script>
	var h1 = document.getElementsByTagName("h1");
    var p1 = document.getElementById("p1");
    var p2 = document.getElementsByClassName("p2");
    
    var div1 = document.getElementById("div1");
    var children = div1.children;//获取父节点下的所有子节点
    var first = div1.fristChild;
    var last = div1.lastChild;
</script>
```

### 更新DOM

```html
<div id="div1"></div>

<script>   
    var div1 = document.getElementById("div1");
    //属性使用字符串包裹
    div1.innerText = "hello,world!";
    div1.innerHTML = "<strong>hello,world!</strong>";
    div1.style.color = "red";
    div1.style.fontSize = "20px";//css中用“-”连接的属性用驼峰命名
    div1.style.padding = "2em"
</script>
```

### 删除DOM

```html
//先获取删除目标元素的父节点，然后通过父节点删除自己
<div id="div1">
	<h1>标题1</h1>
	<p id="p1">段落1</p>
	<p class="p2">段落2</p>
</div>

<script>
    var div1 = document.getElementById("div1");
    var p1 = document.getElementById("p1");
    div1.removeChild(p1);
    
    var p2 = document.getElementByClassName("p2");
    var father = p2.parentElement;
    father.removeChild(p2);
    
    //删除是一个动态的过程，删除多个节点时，children是在时刻变化的，删除节点的时候一定要注意
    father.removeChild(father.children[0]);//删了第一个，剩下的节点依次往前挪
</script>
```

### 插入DOM

```html
<!--我们获得某个DOM节点，假设这个dom节点是空的，我们通过innerHTML就可以增加一个元素了，但是如果这个DOM已经有内容了，就追加。-->
<p id="js">JavaScript</p>
<div id="list">
	<p id="se">JavaSE</p>
	<p id="ee">JavaEE</p>
	<p id="me">JavaME</p>
</div>
<script>
    //把已有标签p放进去
	var js = getElementById("js");
    var list = getElementById("list");
    list.appendChild(js);
    
    //创建新节点然后加进来
    var newP = document.createElement("p");//创建一个p标签
    newP.id = "newP";
    newP.innerText = "hello,i'm newP";
    list.appendChild(newP);
    
    //创建一个script标签节点 & 放进一个自定义属性节点，放到网页头里
    var myScript = document.createElement("script");
    myScript.setAttribute("type","text/javascript");
    document.getElementByTagName("head")[0].appendChild(myScript);
    
    //往之前插
    var ee = document.getElementById("ee");
    list.insertBefore(js,ee);//ee前插入js
</script>
```

### 操作表单

> 常见表单元素：

* 文本框：text
* 下拉框：select
* 单选框：radio
* 多选框：checkbox
* 隐藏域：hidden
* 密码框：password
* ...

```html
<form>
    <label>用户名：</label><input type="text" id="username"/>
    <input id="male" type="radio" name="sex" value="male"/>男
    <input id="female" type="radio" name="sex" value="female"/>女
</form>

<script>
	var input_text = document.getElementById("username");
    console.log(input_text);
    input_text.value = 'hello';
    
    //对于单选框，多选框，value只能取到当前的值，要判断是否选中当前节点，可以用checked属性
    var male_text = document.getElementById("male");
    var female_text = document.getElementById("female");
    if(male_text.checked){
        alert("male is checked!");
    }
    female_text.checked = true;
</script>
```

#### 提交表单高级验证

```html
<form id="myForm" action="" method="post" onsubmit="return aaa();">
    <label>用户名：</label><input type="text" id="username" name="username"/>
    <label>密码：</label><input type="password" id="password" name="password"/>
    <button type="button" onclick="submitForm();">提交</button>
</form>

<!--MD5加密算法工具包-->
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>
<script>
    //两种通过js提交表单的方式
	function aaa(){
        var username = document.getElementById("username");
        var password = document.getElementById("password");
        console.log(username.value);
        //使用md5算法加密
        password.value = md5(password.value);
        console.log(password.value);
        //这里也可以结合正则表达式做前端校验工作
        return true;
    }
    function submitForm(){
        //做各种判断...
        document.getElementById("myForm").submit();
    }
</script>
```





补充一个小点：**钩子函数和回调函数的区别**：

```javascript
//一般认为，钩子函数就是回调函数的一种，其实还是有差异的，差异地方就是：触发的时机不同。
/*特点：
	1. 自己定义的
	2. 自己没有调用
	3. 但它最终执行了
*/
/*常见的回调函数：
	1. dom事件回调偶数
	2. 定时器回调函数
	3. ajax请求回调函数
	4. 生命周期回调函数
*/

//钩子函数
//钩子（Hook）概念源于Windows的消息处理机制，通过设置钩子，应用程序对所有消息事件进行拦截，然后执行钩子函数。
let btn = document.getElementById("btn");
btn.onclick = () => {
    console.log("i'm a hook");
}

//回调函数
//给btn绑定了一个监听器，只有消息捕获完成之后才能触发回调函数。
btn.addEventListener("click",() =>{
    console.log(this.onclick);//undefined
});


//回调函数和钩子函数的一个很明显的差别就是：钩子函数在捕获消息的第一时间就执行，而回调函数是捕获结束时，最后一个被执行的。
```

