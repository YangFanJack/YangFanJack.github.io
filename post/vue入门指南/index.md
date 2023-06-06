
* **vue：** 一款渐进式（逐步实现新特性）JavaScript框架。其特性综合了Angular(**模块化MVVM**)和React(**虚拟DOM**)的优点

* **Ajax：** 前端通信框架，因为Vue的边界很明确，就是为了处理DOM，所以不具备通信能力，需要额外使用一个通信框架和服务器交互；也可以直接选择使用jQuery提供的Ajax同行功能(只是因为为了使用Ajax要完全导入jQuery的包显得很笨重)

* **前端MVVM框架：**

  * 后端中的MVC框架中V：视图层；C：控制层；M：模型层
  * 传统的使用后端模板引擎渲染的方式，V就是指的jsp，freemarker等技术。
  * 前后端分离的概念渐渐出现，特别是在前端框架出现后，前端根据后端的MVC模式，也对前端进行了层次划分，对原来的V转化前端独立渲染。并且也将V扩展为前端自己的MVC三层：V层，M层，VM层。也就是MVVM模式
    * V：view
    * M：data
    * VM：数据双向绑定

* **前端UI框架：**

  * Ant-Desgin：基于React的UI框架
  * ElementUI，iview，ice：基于Vue的UI框架
  * Bootstrap：Twitter推出的前端开发工具包
  * AmazeUI：HTML5跨屏前端框架

* **JavaScript构建工具：**

  * Babel：JS编译工具，主要用于浏览器不支持的ES新特性
  * WebPack：模块打包器

* **NodeJS**

  * 前端人员为了方便开发也需要掌握一定的后端技术，但是java后端异常庞大。所以为了方便前端人员开发后台应用，出现了NodeJS这样的技术。
  * NodeJS由于架构问题和笨重的node_modules，作者已经放弃，不久的将来，Deno可能将会取代NodeJS
  * Express：NodeJS框架
  * Koa：Express简化版
  * NPM：项目综合管理工具
  * YARN：NPM的替代方案，类似于Maven和Gradle的关系

* **vue-element-admin:**

  这是一个集成大部分功能的后台管理系统框架，也就是说大多数的组件都帮我们写好了，他是基于vue-element进行开发布局的。


## MVVM框架

* 源自经典的MVC模式，MVVM的核心是ViewModel层，负责转换Model中的数据对象来让数据变得更容易管理和使用

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230829.png" style="zoom: 67%;" />

### 具体分层：

* Model：模型层，这里表示JavaScript对象
* View：视图层，这里表示DOM
* ViewModel：连接视图和数据的中间件，Vue.js就是MVVM中的ViewModel层的实现者

### ViewModel

* MVVM架构中，不允许数据和视图直接通信，只能通过ViewModel通信，**而ViewModel就是定义了一个Observer观察者**
* ViewModel能观察到数据的变化，并对视图相应内容进行更新
* ViewModel能监听试图的变化，并能够通知数据发生改变

Vue.js的核心是：**DOM监听**&**数据绑定**

* **虚拟DOM：** Vue的ViewModel绑定了数据和视图两层，所以无需操作DOM，工作知识修改DOM中的某些数据而已。这让前端资源大大节省了。
* View层展现的不是Model层的数据，而是ViewModel的数据，由ViewModel负责和Model层交互，这就完全解耦了View层和Model层，这个解耦是至关重要的，它是前后端分离的关键。

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230837.png" style="zoom: 67%;" />

## VUE

**{ { } } & v-text,v-cloak & v-html**

这就是前端的浏览器模板引擎渲染生成html页面。Vue在背后做了大量工作，现在数据和DOM已经建立了关系，所有东西都是响应式的。在控制台操作对象属性后界面能实时更新。

```html
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<!--创建一个容器，使用Vue对象绑定该容器就可以控制这个容器中的所有DOM元素-->
<div id="app">
  <!--插值表达式中可以写任意合法的JS表达式-->
  <!--插值表达式会有闪烁问题，用v-cloak解决-->
  <span>{{ message }}</span>
  <span v-text="message"></span>
  <span v-html="message2"></span>
</div>
```

```javascript
var app = new Vue({//这个对象app就是MVVM中的VM，核心，提供了双向数据绑定的能力
  el: '#app',//和要控制的容器绑定，MVVM中的V视图层
  data: {//要渲染的一些数据，MVVM中的M数据层
    message: 'Hello Vue!'
    message2: '<h1>Hello</h1>'
  }
})
```

### v-bind & v-if,v-else & v-for

```html
<!--在html原始标签属性前加上v-bind:可以将属性和vue的data数据单向绑定，也可以简写只留一个冒号-->
<!--v-bind中要用表达式的拼接需要用引号包裹起来-->
<div id="app">
  <span v-bind:title="message">
</div>
```

* **v-bind：** v-前缀的被成为**指令**，它们是vue提供的一种**特殊标签属性**。它们会在渲染的DOM上应用特殊的响应式行为。意思就是：将这个元素的节点的title特性和vue实力的message属性保持一致

* **判断** 和 **循环** 指令：**v-if**和**v-else**

  ```html
  <!-- 生产环境版本，优化了尺寸和速度 -->
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
  
  <div id="app">
    <h1 v-if="ok=='A'">A</h1>
    <h1 v-else-if="ok='B'">B</h1>
    <h1 v-else>C</h1>
  </div>
  
  <!--只要涉及到v-for这种循环，推荐给每一项都加上:key属性，并且:key属性只接受number或string类型唯一的数据-->
  <div id="vm">
    <ul>
       <li v-for="(item,i) in items" :key="item">
          {{i}}-----{{item.message}}  
       </li> 
  </ul>
  </div>
  ```

  ```javascript
  var app = new Vue({
    el: '#app',
    data: {
      ok: 'B'
    }
  })
  
  var vm = new Vue({
    el: '#vm',
    data: {
      items:[
          {message: '哈哈哈'},
          {message: '嘿嘿嘿'}
      ]
    }
  })
  ```

* 一般而言，v-if 有更高的切换消耗而v-show 有更高的初始渲染消耗。因此如果需要频繁切换,v-show更好，如果在运行时条件不改变v-if较好

### 事件v-on

* 事件一定要定义在Vue对象中的methods中

```html
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">
  <button v-on:click="sayHi"></button>
  <!--简写-->
  <button @click="sayHi2"></button>
</div>
```

```javascript
var app = new Vue({
  el: '#app',
  data: {
      message: '人生如梦'
  },
  methods: {//methods属性中可以定义多个事件处理函数
  	sayHi: function(){
    	alert(this.message);//要访问data中的值要用this,this代表vm实例app
    }
    //简写
    sayHi2(){
    	alert(this.message);
    }
  }
})
```

### v-model实现双向绑定

**v-bind** 产生的效果**不含有双向绑定**，所以 :value 的效果就是让 input的value属性值等于 data.name 的值，而 **v-model** 的效果是使input和 data.body **建立双向绑定**，因此首先 data.body 的值会给input的value属性，其次，当input中输入的值发生变化的时候，data.body 还会跟着改变。

```html
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">
  <!--vue中，只有v-model实现了事项绑定-->
  <!--vue中，v-model只能应用到表单元素中-->
  <input type="text" v-model="message">{{message}}
</div>
```

```javascript
var app = new Vue({
  el: '#app',
  data: {
      message: '123'
  }
})
```

* **双向数据绑定目的：** 为了修改表单后不用自己操作DOM元素获取表单元素的值。
* **单项数据绑定目的：** 为了不用自己操作DOM去渲染数据。

### 事件修饰符

* @click**.stop**:组织事件冒泡
* @click**.prevent**:组织标签默认行为，例如a的href，form的submit
* @click**.capture**:将默认的冒泡模式改为捕获模式
* @click**.self**:只有事件在该元素身上触发才会调用
* @click**.once**:事件只触发一次

### vue中使用类样式的4种方式

```html
<!--red，small,thin,italic等是css样式类-->
<!--第一种-->
<h1 :class="['red','small']">Hello,world!</h1>
<!--第二种-->
<h1 :class="['red',isThin?'thin':'']">Hello,world!</h1>
<input type="button" value="变瘦" @click="isThin=true">
<!--第三种-->
<h1 :class="['red',{thin:isThin}]">Hello,world!</h1>
<!--第四种-->
<h1 :class="{red:true,italic:true,thin:isThin}">Hello,world!</h1>

var app = new Vue({
  el: '#app',
  data: {
      isThin: false
  }
})
```

* **一般最常用第二个和第三个实现CSS类的动态切换**

### vue中行内style样式

```html
<h1 :style="{color:'red','font-size':'italic'}">Hello,world!</h1>
```

### 例子

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 生产环境版本，优化了尺寸和速度 -->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <link rel="stylesheet" href="node_modules/bootstrap/dist/css/bootstrap.min.css">
</head>
<body>
    <div id="app">
        <div>
            <label>Id:</label>
            <input type="text" v-model="id">
            <label>Name:</label>
            <!--注意键盘keyup修饰符:enter，还有tab,delete,space,esc,up,down,left,right-->
            <input type="text" v-model="name" @keyup.enter="add">
            <input type="button" value="添加" class="btn btn-primary" @click="add">

            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <label>按照商品名称检索商品:</label>
            <input type="text" v-model="keyword" @change="searchx">
        </div>
    
        <table class="table table-bordered table-hover table-striped">
            <thead>
                <tr>
                    <th>Id</th>
                    <th>Name</th>
                    <th>CTime</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in searchx()" :key="item.id">
                    <td>{{ item.id }}</td>
                    <td>{{ item.name }}</td>
                    <td>{{ item.ctime }}</td>
                    <td>
                        <a href="https://www.baidu.com" @click.prevent="del(item.id)">删除</a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                id:"",
                name:"",
                keyword:"",
                list: [
                    {id:1,name:'丰田',ctime:new Date()},
                    {id:2,name:'奔驰',ctime:new Date()}
                ]
            },
            methods: {
                add(){
                    this.list.push({id:this.id,name:this.name,ctime:new Date()});
                    this.id="";
                    this.name="";
                },
                del(id){
                    for(let i = 0;i < this.list.length;i++){
                        if(this.list[i].id==id){
                            this.list.splice(i,1);
                            break;
                        }
                    }
                },
                searchx(){
                    let result=[];
                    for(let i=0;i<this.list.length;i++){
                        if(this.list[i].name.indexOf(this.keyword)!=-1){
                            result.push(this.list[i]);
                        }
                    }
                    return result;
                }
                
            }
        });
    </script>
</body>
</html>
```

### 过滤器

* Vue.js中允许你自定义过滤器，可以用作常见的文本格式化

* 可以用在两个地方：插值表达式 & v-bind 表达式

* 自定义全局过滤器

  ```html
  <!--插值表达式中加入管道符-->
  {{item.ctime | formatDate}}
  <!--设置全局过滤器-->
  Vue.filter({
  	"formatDate",
  	function(data){//这个回调函数的第一个参数默认永远是管道符前面的那个值
  	    return moment(data).format("YYYY-MM-DD HH:mm:ss");
      }
  })
  ```

* 私有过滤器

  ```javascript
  var app = new Vue({
    el: '#app',
    data: {
        msg:"春节"
    },
    methods: {},
    filters: {
        addStr: function(data){
            return data+""
        }
    }
  })
  ```

  全局和私有过滤器如果重名，会先用自己私有的。

### 自定义指令

```javascript
//全局自定义获得焦点的on-focus指令
//注意：Vue自定义指令的名称中，不需要v-前缀，但是在调用自定义指令时，需要在前面加上v-前缀
Vue.direct('focus',{
    //参数中的第一个参数永远是被绑定的那个元素
    bind: function(el,binding){//当指令绑定的元素被vue实例解析时执行
        binding.name;//获取指令的名称
        binding.value;//获取指令等号后面的值
    },
    inserted: function(el){//当指令绑定的元素被插到父节点时调用
        el.focus();
    }
})

//自定义私有属性
var vm = new Vue({
    el: "#app",
    data: {},
    methods: {},
    filters: {},
    directives: {
        bold: {
            bind(el,binding){
                
            },
            inserted(el){
                
            }
        }
    } 
})
```

### 计算属性

* 这是一个有计算能力的属性，用来代替复杂表达式，计算实际上就是一个函数
* 实际上是一个**能将计算结果缓存起来的属性**（将行为转化成了静态的属性），可以理解为缓存。
* computed和methods的区别是：一个是方法，一个是属性。
* 在浏览器中f12调试：
  * vm.currentTime1：每次打印结果都不一样
  * vm.currentTime2：每次打印结果都和第一次一样.只有currentTime2计算中有东西发生了改变，才会重新刷新数据，这和缓存一模一样。
* 计算属性的主要特性就是为了将不经常变化的计算结果进行缓存，以节约我们的系统开销。尤其是高并发系统。

```html
<div id="app">
    <p>currentTime1 {{currentTime1()}}</p>
    <p>currentTime2 {{currentTime2}}</p>
</div>
```

```javascript
var vm = new Vue({
    el: "#app",
    data: {
        message: "hello"
    },
    methods: {
        currentTime1: function(){
            return Date.now();
        }
    }
    computed: {
    	currentTime2: function(){
    		return Date.now();
		}
	}
})
```

### vue生命周期

* 创建阶段：一个实例整个生命只执行一次
* 运行阶段：一个实例在整个生命周期中根据data改变与否有选择地执行0到多次
* 销毁阶段：一个实例整个生命只执行一次
* 在实例每个阶段中会伴随各种事件，这些事件统称为实例的 **生命周期函数 / 生命周期钩子 / 生命周期事件**，这些函数是vue自己执行，但是内容可能要自己写。
* 在new Vue()时，主要做了以下的事：
  1. 初始化data和methods
  2. 根据数据在内存中渲染出一棵DOM树
  3. 把创建好的DOM树挂载到页面上显示给用户【创建页面结束】
  4. 根据data数据的变化，有选择性的渲染DOM树
  5. 把重新渲染的DOM树再次挂载到页面上，从而保持data界面的同步
* 操作data和methods最早能在created中操作
* 操作DOM元素最早能在mounted中操作

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230850.png" style="zoom: 67%;" />

```javascript
var vm = new Vue({
　　el:'#app',
    data: {},
    methods: {},
　　beforeCreate:function(){
　　　　console.log('before create')
　　},
　　created:function(){
　　　　console.log('created')
　　},
　　beforeMount:function(){
　　　　console.log(this.$el);
　　　　console.log('before mount')
　　},
　　mounted:function(){
　　　　console.log(this.$el);
　　　　console.log('mounted')
　 },
　　beforeDestroy:function(){
　　　　console.log('beforeDestroy')
　　},
　　destroyed:function(){
　　　　console.log('destroyed')
　　},
　　beforeUpdate:function(){
　　　　console.log('before updated')
　　},
　　updated:function(){
　　　　console.log('updated')
　　}
})
```

**注意：最常用的生命周期函数是created函数，我们会在created钩子函数中：从服务器获取数据，并对数据进行初始化。**

### vue常用7大属性

1. el：指示vue编译器从什么地方开始解析vue的语法，可以说是一个占位符
2. data：组织从view中抽象出来的属性，可以说将视图的数据抽象出来放到data中
3. template：用来设置模板，会替换页面元素，包括占位符
4. methods：放置页面中的业务逻辑，js方法一般都放置在methods中
5. render：创建真正的Virtual Dom
6. computed：用来计算
7. watch：
   * watch:function(new,old)()
   * 监听data中数据的变化
   * 两个参数，一个返回新值，一个返回旧值

### Vue组件

实际开发中，不会用以上方式开发组件，而是采用vue-cli创建.vue模板文件的方式开发，以上方法只是为了说明啥是组件。

组件实际上就是一个自己定义标签元素的工具

```html
<div>
    <comp-jack></comp-jack>
</div>
```

```javascript
//全局组件注册
Vue.component("comp-jack",{//组件名字所有字母小写，不用驼峰命名法（如果两个单词，中间用-连接）
    template: '<li>Hello</li>',
    //组件中的data需要定义为函数的原因：
    //组件会被多次使用，多以data语法上必须是一个函数，每次使用时返回一个新的对象
    //组件要在Vue实例中使用，所以最后一定要创建Vue实例
    //组件中template的html语句中只能有一个根标签
    data: function(){
      	return {
            count: 0
        }  
    },
    methods: {
        increment(){
            this.count++;
	    }
    }
});

//局部组件注册
const vm = new Vue({
    el: "#app",
    data: {},
    components: {
        "组件名":{
            template:"复用的html片段",
            data: function(){
                return {}//return的对象，类似创建Vue时的data
            },
            methods:{
                //用于定义函数
            }
        }
    }
})

//局部组件注册的更合理的写法1（结构更加清晰）
let temp1 = {
    template:"复用的html片段",
    data: function(){
        return {}//return的对象，类似创建Vue时的data
    },
    methods:{
        //用于定义函数
    }
}
let temp2 = {
    template:"复用的html片段",
    data: function(){
        return {}//return的对象，类似创建Vue时的data
    },
    methods:{
        //用于定义函数
    }
}
const vm = new Vue({
    el: "#app",
    data: {},
    components: {
        "组件名1": temp1,
        "组件名2": temp2
    }
})

//局部组件注册的更合理的写法1（结构更加清晰）
<template id="t1">
      复用的html片段
</template>

const vm = new Vue({
    el: "#app",
    data: {},
    components: {
        "组件名":{
            template:"#t1",
            data: function(){
                return {}//return的对象，类似创建Vue时的data
            },
            methods:{
                //用于定义函数
            }
        }
    }
})
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 生产环境版本，优化了尺寸和速度 -->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>
<body>
    <h1>大标题</h1>
    <div id="app">
        <jack v-for="item in items" v-bind:j="item"></jack>
    </div>
    <script>
        Vue.component("jack",{
            props: ['j'],
            template: '<li>{{j}}</li>'
        });

        var app = new Vue({
            el: '#app',
            data: {
               items: ["Java","Linux","Windows"]
            }
        });
    </script>
</body>
</html>
```

#### 组件的嵌套

* 在实际页面开发中很多组件都是存在嵌套行为的
* 顶层的组件直接在Vue对象实例中注册
* 如果一个组件时组件的组件，那么它应该是在组件的components属性中注册，一次类推可以得到多层组件的嵌套
* **注意：Vue中一切皆组件，最大的Vue实例实际也是一个组件**

```html
<div id="app">
    <my-table></my-table>
</div>
<template id="table-head">
	<thead>
    	<tr>
        	<th>id</th>
            <th>name</th>
            <th>age</th>
        </tr>
    </thead>
</template>
<template id="table-body">
	<tbody>
    	<tr>
        	<th>1</th>
            <th>Jack Yang</th>
            <th>21</th>
        </tr>
        <tr>
        	<th>2</th>
            <th>Sam Smith</th>
            <th>28</th>
        </tr>
    </tbody>
</template>
<template id="my-table">
	<table border="1">
        <thead is="table-head"></thead>
        <thead is="table-body"></thead>
    </table>
</template>
```

```javascript
const tableHead = {
    template: "#table-head"
};
const tableBody = {
    template: "#table-body"
};
const myTable = {
    template: "#my-table",
    components: {
        "table-head": tableHead,
        "table-body": tableBody
    }
};
const vm = new Vue({
    el: "#app",
    components: {
        "my-table": myTable
    }
})

```

### 组件通信

一个复杂的界面一定有组件的嵌套，这时一定也会有组件之间通信的需求

#### 组件通信—父传子

* **props：**父组件向子组件正向传递参数通过props来实现

```html
<div id="app">
    <my-table v-bind:us="users"></my-table>
</div>
<div id="app2">
    <my-table v-bind:us="users"></my-table>
</div>
<template id="my-table">
	<table border="1">
        <thead>
        	<tr>
        		<th>id</th>
            	<th>name</th>
            	<th>age</th>
        	</tr>
        </thead>
        <tbody>
        	<tr v-for="u in us" :key="u.id">
        		<th>{{ u.id }}</th>
            	<th>{{ u.name }}</th>
            	<th>{{ u.age }}</th>
        	</tr>
        </tbody>
    </table>
</template>
```

```javascript
const myTable = {
    template: "#my-table",
    props: ["us"]
}
const vm = new Vue({
    el: "#app",
    components: {
        "my-table": myTable
    }
    data: {
    	users: [
    		{id:1,name:"jack1",age:11},
             {id:2,name:"jack2",age:12},
             {id:3,name:"jack3",age:13}
    	]
	}
})
```

#### 组件通信—子传父

* 父组件的模板中包含子组件，经常会出现子组件的状态发生改变时，要通知到父组件。所有的prop都是的父子prop之间i形成了**单向下行绑定**。父级prop更新会向下更新到子组件中。但是反过来不行
* 举个栗子：就是上面中，子组件接收到了us，如果在子组件中改变了us的值，但是父组件中的users不会更改。这种更新只能是上级影响下级，下级不能影响上级。
* **$emit：** 子传父只能通过方法来传值

```html
<!--父组件标签中绑定一个自定义事件,事件处理函数定义在父组件上，并且函数的参数就是子组件的$emit传递来的数据的形参-->
<counter @自定义事件名="自定义事件处理函数"></counter>
<!--子组件中主动发射-->
methods: {
	increment(){
		方法处理代码;
		this.$emit("自定义事件名",传递的数据);
	}
}
```

#### 父子通信实战：

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230904.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>
<body>
    <div id="app">
        <my-table :us="users" @emit-show-user="handleShowUser"></my-table>
        <hr>
        <update-form :u="user" @emit-update-user="handleUpdateUser"></update-form>
    </div>

    <template id="my-table">
        <table border="1">
            <thead>
                <tr>
                    <th>id</th>
                    <th>name</th>
                    <th>age</th>
                    <th>operation</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(user, index) in us" :key="index">
                    <th>{{ user.id }}</th>
                    <th>{{ user.name }}</th>
                    <th>{{ user.age }}</th>
                    <th>
                        <button @click="emitUser(index)">更新</button>
                    </th>
                </tr>
            </tbody>
        </table>
    </template>

    <template id="update-form">
        <form action="" @submit.prevent="handleSubmit">
            <input type="hidden" name="id" v-model="u.id">
            名字：<input type="text" name="name" v-model="u.name"><br>
            年龄：<input type="text" name="age" v-model="u.age"><br>
            <input type="submit" value="更新">
        </form>
    </template>
    
    <script>
        const myTable={
            template: "#my-table",
            props: ["us"],
            methods: {
                emitUser(index){
                    this.$emit("emit-show-user",this.us[index]);
                }
            }
        }

        const updateForm={
            template: "#update-form",
            props: ["u"],
            methods: {
                handleSubmit(){
                    this.$emit("emit-update-user",this.u);
                }
            }
        }

        const vm = new Vue({
            el: "#app",
            data: {
                users: [
                    {id:1,name:"Jack",age:18},
                    {id:2,name:"Mariah",age:19},
                    {id:3,name:"Sam",age:20}
                ],
                user: {}
            },
            components: {
                "my-table":myTable,
                "update-form":updateForm
            },
            methods: {
                handleShowUser(u){
                    console.log(u);
                    this.user = JSON.parse(JSON.stringify(u));
                },
                handleUpdateUser(u){
                    console.log(u);
                    this.users.forEach((user,i,arr)=>{
                        if(user.id==u.id){
                            //vue监控不到直接通过下标修改元素的操作
                            // arr[i]=u;
                            this.$set(arr,i,JSON.parse(JSON.stringify(u)));//等同于arr[i]=u，但是可以被vue监控到
                        }
                    })
                }
            }
        })
    </script>
</body>
</html>
```

## 路由

* 路由：让请求按照其地址到达目的地

### web路由的发展

* 网站路由的发展实际上和前后端分离的发展是同步的：

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230950.png" style="zoom: 50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230957.png" style="zoom: 50%;" />

### Vue中路由的作用：

* 在Vue中的一个功能视图就是一个组件，Vue中的路由解决的是但页面中组件的显示切换问题。比如：一个页面中有两个超链接“登录”,“注册”,点击两个连接分别显示对应的表单

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231042.png" style="zoom: 67%;" />

### 第一个路由示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script src="https://unpkg.com/vue-router@3.0.1/dist/vue-router.min.js"></script>
</head>
<body>
    <div id="app">
        <hr>
        <a href="#/login">登录</a>
        <a href="#/register">注册</a>
        <hr>
        <div>I am a div</div>
        <!-- 使用路由，确定组件切换后显示的位置 -->
        <router-view></router-view>
    </div>

    <!-- 定义组件 -->

    <template id="login-form">
        <form action="">
            Username：<input type="text"><br>
            Password：<input type="text"><br>
            <input type="submit" value="Login"><br>
        </form>
    </template>

    <template id="register-form">
        <form action="">
            Username：<input type="text"><br>
            Password：<input type="text"><br>
            RePassword：<input type="text"><br>
            <input type="submit" value="Register"><br>
        </form>        
    </template>
    
    <script>
        //定义组件
        const loginForm = {
            template: "#login-form",
        }

        const registerForm = {
            template: "#register-form",
        }
        //创建路由对象，定义路由规则
        const MyRouter = new VueRouter({
            routes: [
                {name:"login",path:"/login",component:loginForm},
                {name:"register",path:"/register",component:registerForm}
            ]
        })
        //在Vue实例中配置router
        const vm = new Vue({
            el: "#app",
            router: MyRouter
        })
    </script>
</body>
</html>
```

### router-link & redirect

```html
<!--此时，在连接前不用加上#了-->
<router-link to="/register">注册</router-link>

<!--进入页面默认跳到某个组件-->
<!--在路由对象中定义routes时候-->
<script>
    routes: [
        {name:"login",path:"/",redirect:"/login"},//进入/时，默认执行/login路由
        {name:"login",path:"/login",component:loginForm},
        {name:"register",path:"/register",component:registerForm}
    ]
</script>
```

### 嵌套路由

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231051.png" style="zoom:67%;" />

```html
<!--要使点击第二层的router-link后显示的内容在第二层的router-view里，而不是第一层的router-view里，需要在路由对象中定义routes的时候，将第二层的路由信息写在父路由信息中-->
<script>
    routes: [
        {name:"login",path:"/",redirect:"/login"},//进入/时，默认执行/login路由
        {
            name:"login",
            path:"/login",
            component:loginForm,
            children:[
                {name:"phoneLogin",path:"/phone",component:phoneLoginForm},
                {name:"accountLogin",path:"/account",component:accountLoginForm},
            ]
        },
        {name:"register",path:"/register",component:registerForm}
    ]
</script>

```

### 路由传参

#### query传参

* 导航路由地址里用`?name=jack&age=18`传参，在组件中要获得参数，需要使用内置属性：`{{$route.query.name}}`,`{{$route.query.age}}`

* 在router-link中还可以只用如下方式动态传参：

  ```html
  <router-link :to="{path:'/login/account',query:{name:'Jack',age:18}}"></router-link>
  ```

#### params传参

```javascript
//这个:name代表他能动态地匹配数据
//属于restful风格（路径中包含一部分数据）
{name:"product",path:"/product/:name",component:myComponent}
//通过如下方式获取
{{ $route.params.name }}
```

* 在router-link中可使用如下方式传参：

```html
<router-link :to="{name:'product',params:{name:'Jack'}}"></router-link>
```

### 编程式的路由导航

```javascript
$router.push("/login");
```

## Axios

* Axios是一个开源的可以用在浏览器端和NodeJS的异步通信框架，它的主要作用是实现AJAX异步通信。
* 由于Vue.js是一个视图层框架，作者严格遵循SoC原则，所以Vue.js没有AJAX通信功能，为了解决通信功能。作者单独开发了一个vue-resource插件。但是进入vue2.0后停止了对该插件的维护并推荐Axios插件。
* 对于AJAX通信，要少用JQuery，因为它对DOM的操作过于频繁

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231102.png" style="zoom:80%;" />

### 针对GET的Axios请求

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231108.png" style="zoom:80%;" />

### 针对POST的Axios请求

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231125.png" style="zoom:80%;" />

### Axios的别名方法

```javascript
axios.get(url,{
    params:{
        username:this.username,
        password:this.password
    }
})

axios.post(url,Qs.stringify({
    username:this.username,
    password:this.password
}))
```

## vue-cli

* vue-cli（Vue command-line-interface）是官方提供的一个脚手架，用于快速开发生成一个vue的项目模板

* 脚手架就是预先定义好的目录结构和基础代码，就像创建maven项目时可以选择创建一个初始骨架项目。这种脚手架能让我们开发更加迅速。

* 主要功能：

  * 统一的目录结构
  * 本地调试
  * 热部署
  * 单元测试
  * 集成打包上线


```shell
cnpm install vue-cli -g
vue init webpack myvueName

#vue-cli的3.0+以后使用的不是vue-cli了，如果用以上的安装命令安装的并不是最新版的3.0+的，而如果安装3.0的话就需要使用新的
cnpm install @vue/cli -g
```

* **Vue-cli项目的目录分析：**

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231138.png" style="zoom: 67%;" />

  * bulid：配置好了的文件，保存的是webpack打包工具的底层配置
  * config：关于vue的配置（端口号啥的）
  * node_modules：项目依赖的各个包，类似于java的lib目录
  * src：代码储存地，我们写代码的地方
    * assets：静态资源（图片）
    * component：存放一个个的组件，.vue文件表示一个组件
    * router：路由文件
    * App.vue：根组件vue文件
    * main.js：整个项目的入口
  * static：放一些静态的文件，不建议使用，一般被assets替代
  * index.html：vue的入口页面，只有一个div标签
  * package.json：相当于maven中的pom.xml文件，包管理文件

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521231145.png" style="zoom: 80%;" />

### .vue文件

传统的template定义方式：先定义一个template标签，然后全局或者私有地定义一个component。这种方式在很多中小规模的项目中运行的很好，这些项目中JavaScript之被用来加强特定的视图，但是当更加复杂的项目中，当你的前端完全由JavaScript驱动的时候，以下的缺点将会非常明显：

* 全局定义会强制要求每个component中的命名不重复
* 不支持CSS意味着当HTML和JavaScript组件化时，CSS明显被遗弃
* 可读性差，维护难度高，多个组件定义在一起，代码显得更加臃肿复杂

基于以上传统的vue组件的定义方式，.vue结尾的vue专属文件single-file-components（单文件组件）为以上所有问题提供了解决方法。

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
    //使用export标签将组件导出，需要使用组件的文件就可以使用import导入
    export default {
      name: 'App'
    }
</script>

<style>
    #app {
      font-family: 'Avenir', Helvetica, Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      text-align: center;
      color: #2c3e50;
      margin-top: 60px;
    }
</style>
```

### 总结（基于脚手架开发の规约）

* index.html日后不会被修改
* App.vue作为根组件，定义了页面的基本结构，日后开发中，根据需求修改它
* 在components中新建了.vue文件来定义组件
* 在router/index.js中定义了路由规则
* 在main.js中引入了项目需要的库

### 基于Vue-cli和SSM搭建前后端分离项目

* 安装axios和qs库

  ```shell
  npm install --save axios vue-axios qs
  ```

* 引入 & 配置包供全局使用

  ```javascript
  import qs from 'qs'
  import axios from 'axios'
  import VueAxios from 'vue-axios'
  
  //在所有的Vue组件中可以通过使用this.$qs使用qs库
  Vue.prototype.$qs = qs
  //配置后端服务地址（axios的默认请求前缀）
  axios.defaults.baseURL = "http://localhost:8080"
  //在Vue中配置axios，在所有Vue组件中可以通过this.axios使用axios库
  Vue.use(VueAxios,axios)
  ```

* **跨域问题**

  * 由于浏览器的同源策略限制，当一个请求url（后端部署服务器的地址）的**协议、域名、端口**三者之间任意一个与当前页面url（前端部署服务器的地址）不同即为跨域

  F12控制台报错显示：

  ```javascript
  Access to XMLHttpRequest at 'http://localhost:8080/show?pageNum=1&pageSize=3' from origin 'http://localhost:8081' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
  ```

  * 跨域问题的解决：（服务端和浏览器端都要做响应配置）

    * 前端配置：

      ```javascript
      //main.js中添加如下配置
      axios.defaults.withCredentials = true
      ```

    * 服务端配置

      ```java
      //添加响应头
      //设置允许跨域共享资源的前端地址
      resp.setHeader("Access-Control-Allow-Origin","http://localhost:8080");
      //允许client跨域请求时携带cookie
      resp.setHeader("Access-Control-Allow-Credentials","true");
      ```

      ```java
      //也可以将以上代码做成过滤器并在web.xml中配置过滤器
      public class AccessControlAllowFilter implements Filter {
          @Override
    public void init(FilterConfig filterConfig) throws ServletException {
      
          }
      
          @Override
          public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
              HttpServletResponse response = (HttpServletResponse) servletResponse;
              //设置允许跨域共享资源的前端地址
              response.setHeader("Access-Control-Allow-Origin","http://localhost:8081");
              //允许client跨域请求时携带cookie
              response.setHeader("Access-Control-Allow-Credentials","true");
      
              filterChain.doFilter(servletRequest,servletResponse);
          }
      
          @Override
          public void destroy() {
      
          }
      }
      ```
      
      ```xml
      <filter>
          <filter-name>AccessControlAllowFilter</filter-name>
          <filter-class>
              jack.filter.AccessControlAllowFilter
          </filter-class>
      </filter>
      <filter-mapping>
          <filter-name>AccessControlAllowFilter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
      ```

## Webpack

* webpack本质上是一个现代的Javascript应用程序的静态模块打包器。它会递归构建一个依赖关系图，其中包含应用程序所需的每个模块，然后将所有模块构成一个或多个bundle
* webpack是当下最热门的前端资源模块化管理和打包工具。它可以将许多松散耦合的模块按照依赖和规则打包成符合生产环境部署的前端资源。还可以将按需加载的模块进行代码分离，等到实际需要时再异步加载。通过loader转换，任何形式的资源都可以当作模块。
* 移动互联网来临，更多的网页进化到了WebApp模式。它通常是一个SPA（单页面应用），每一个视图通过异步的方式加载，页面初始化和使用过程中会导致越来越多的JavaScript代码，这回给前端的开发流程和资源组织带来巨大压力。

### 安装WebPack

webpack是一款模块加载器兼打包工具，他能把各种资源：如JavaScript，JSX，ES6，SASS，LESS，图片等资源作为模块来处理和使用。打包后直接把ES6规范降级为ES5了，这样所有浏览器就都支持了。

```shell
cnpm install webpack -g
cnpm install webpack0-cli -g
#验证安装是否成功
webpack -v
webpack-cli -v
```

* **webpack.config.js** 配置文件解析
  * entry：入口文件，制定webpack用哪个文件作文项目的入口
  * output：输出，制定webpack将处理后的文件放在哪里
  * modlule：模块，用于处理各种类型的文件
  * plugin：插件，如：热更新，代码重用
  * resolve：设置路径指向
  * watch：监听，用于设置文件改动后直接打包
