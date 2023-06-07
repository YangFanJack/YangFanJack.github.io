# 精简版JS:jQuery


## JavaScript 和 JQuery

jQuery就是一个JavaScript库，里面封装了大量JavaScript函数

推荐一个各种开源项目CDN服务搜索网站：`https://www.bootcdn.cn/`

```html
<a href="" id="text-jquery">click me</a>

<!--JavaScript的在线cdn引入-->
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.js"></script>
<script>
	//公式：$(css的selector).action();
    //document.getElementById("text-jquery").onclick = function(){}
    $("#text-jquery").click(function(){
        alert("hello,jquery");
    })
</script>
```

## $是什么

```javascript
//jQuery的文件结构：其实是一个自执行函数,引入了jquery的js文件，会执行这个自执行函数。
(function(){
    window.jQuery = window.$ = jQuery;//给window对象添加一个jQuery属性和$属性，用JQuery函数赋值给$
    //$是一个函数，所以会根据参数的不同进行不同的处理
    $(function(){});//入口函数
    $("#div");//选择器
    $("<div>我是一个div标签</div>");//创建div标签
    $(dom对象);//把dom对象转化为jquery对象
})
```

## Dom对象和jQuery对象

dom对象：原生js选择器获取到的对象，只能调用dom属性或者方法，但是不能调用jQuery的属性和方法

```javascript
var div1 = document.getElementById("div1");
div1.style.backgroundColor = "red";
```

jQuery对象：由jQuery选择器获取到的对象，只能调用jQuery的属性或者方法，但是不能调用原生js的属性和方法

```javascript
var div1 = $("#div1");
div1.css("backgroundColor","red");
```

jquery对象是一个伪数组，其实是dom对象的一个包装集

jQuery和JavaScript对象的互相转化：

```javascript
//JavaScript对象——》jQuery对象
var div1 = document.getElementById("div1");
var $div1 = $(div1);
$div1.css("backgroundColor","red");

//jQuery对象——》JavaScript对象
var $div1 = $("#div1");
var div1 = $div1[0];
var div2 = $div1.get(0);
```

## 选择器

```javascript
//原生JavaScript
//标签
document.getElementsByTagName();
//id
document.getElementById();
//类
document.getElementsByClassName();

//jQuery选择器
$("p");//标签
$("#id1");//id
$(".class1");//类
//......(css中所有选择器都适用：基本选择器，层次选择器，伪类，伪元素，属性选择器...)

//还有一些筛选方法来过滤
children(selector);//子类选择器
find(selector);//后代选择器
siblings(selector);//兄弟节点
parent();//父亲
eq(index);//索引
next();//下一个兄弟
prev();//上一个兄弟
```

## 事件

鼠标事件，键盘事件，其他事件

```javascript
blur()//元素失去焦点
change()//用户改变域的内容
click()//鼠标点击某个对象
dbclick()//鼠标双击某个对象
error()//加载文档或者图形时发生错误
focus()//元素获得焦点
keydown()//用户摁下摁键时发生
keypress()//用户摁下摁键，并且产生一个字符时发生
keyup()//用户释放某一个摁键时触发
load()//某个页面或图像完成加载
mousedown(//某个鼠标按键被按下
mousemove()//鼠标被移动
mouseout()//鼠标从某元素移开
mouseover()//鼠标被移到某元素上(子元素也会触发)
mouseenter()//鼠标必须点到选取的元素上才触发，注意和mouseover区别
mouseup()//某个鼠标按键被松开
resize()//窗口或者框架被调整大小
scroll()//滚动文档的可视部分
select()//文本被选中
submit()//提交按钮被点击
unload()//用户退出界面

//有的函数通过回调函数中的参数获取更多操作的细节：键盘鼠标按键wihch，mouse移动横纵坐标
```

```javascript
//入口函数
//window.onload() 方法用于在网页加载完毕后立刻执行的操作，即当 HTML 文档加载完毕后，立刻执行某个方法。
window.onload=function(){//入口函数唯一
    Func1();
    Func2();
    Func3();
    ...
}
//$(document).ready()注意在body中没有onload事件，否则该函数不能执行。在每个页面中可以有很多个函数被加载执行，按照fn的顺序来执行。
$(document).ready(function(){//入口函数可以不唯一
    alert("网页已经加载完成");
})
//简化为
$(function(){
	alert("网页已经加载完成");
})
    

//window.onload和$(function(){})的执行流程是jQuery先执行
//$(function(){})要等待页面上dom树加载完毕后执行，window.onload要等待页面上所有资源都加载完毕后再执行
```

### on注册事件

jQuery1.7之后，jQuery用on统一了所有时间的处理方法，之后都建议使用on来注册事件

```javascript
//on注册简单事件，不支持动态绑定
$(selector).on("click",function(){});

//on注册事件委托(selector是子元素的父元素，所以必须是selector的内部子元素才能触发这个事件，支持动态绑定)
$(selector).on("click","子元素",function(){});
```

```javascript
//使用off()来解绑事件
$(selector).off();//解绑所有事件
$(selector).off("click");//解绑指定事件
```

### 事件触发

自定义事件发生的时机条件

```javascript
//自定义条件的时候执行click函数内容
//两种方法都可以。
$(selector).click();
$(selector).trigger("click");

//对于自定义事件，就只能使用触发器来了
$(selector).on("jack",function(){
    console.log("hello,jack!");
})
$(selector).trigger("jack");
```

### 事件对象

> 什么是事件对象

注册一个事件，系统会帮我们生成一个对象记录这个事件触发时候的一些信息

比如触发事件的时候有没有按住某个键，以及一些坐标信息

jQuery中的事件对象由形参e来获取，jQuery中的事件对象是对js原生事件对象的封装，处理好了浏览器兼容性

```javascript
$(selector).on("click",function(e){
    //距离屏幕左上角的值
    console.log(e.screenX+" : "+e.screenY);
    //距离页面坐上角的值
    console.log(e.clientX+" : "+e.clientY);
    //距离页面最顶部左上角的值
    console.log(e.pageX+" : "+e.pageY);
    
    //阻止事件冒泡
    e.stopPropagation();
    //阻止浏览器默认行为
    e.preventDefault();
    //上面两个同时阻止
    return false;
    //按下的键盘代码
    e.keyCode;
    e.which;
})
```



## 操作DOM

```html
<ul id="test-ul">
    <li class="js">JavaScript</li>
    <li name="python">Python</li>
</ul>

<script>
	$("#test-ul li[name=python]").text();//获得值
    $("#test-ul li[name=python]").html();
    $("#test-ul li[name=python]").text("派森");//获得值
    $("#test-ul li[name=python]").html("<em>派森</em>");//获得值
    $("input元素名称").val();//获取input元素的值
	$("input元素名称").val(value);//设置input元素的值为value
    
    $(selector).attr("属性名");//获取属性
    $(selectorselector).attr("属性名","属性值");//设置属性
    $(selector).removeAttr("属性名称");//给某元素删除指定的属性以及该属性的值
    $(selector).addClass("class1 class2");//给某元素添加指定的样式
	$(selector).removeClass("class1 class2");//给某元素删除指定的样式
    $(selector).toggleClass("class1 class2");//给某元素切换指定的样式
    $(selector).hasClass("class");//判断是否有样式
    //对于checked,selected,disabled这类boolean类型的属性来说，不能用attr方法获取属性值，只能用prop方法
    $("input:checked").prop("check");//返回true或false
    $("input:checked").prop("check",true);
    
    //css操作
    $("#test-ul li[name=python]").css("color","red");
    $("#test-ul li[name=python]").css({
        "width": "100px",
        "backgroundColor": "red"
    });
    
    //显示&隐藏（本质是设定display）
    $("#test-ul li[name=python]").show();
    $("#test-ul li[name=python]").hide();
    $("#test-ul li[name=python]").toggle();
    
    //宽高
    //不带padding，margin和border
    height();//直接返回200，没有px，如果用css返回值带px单位
    height(100);//不带单位默认px
    widtn();
    //带padding
    innerWidth();
    innerHeight();
    //带padding和border
    outerWidth();
    outerWidth();
    //带padding，border和margin
    outerWidth(true);
    outerWidth(true);
    //获取网页可视区的宽高
    $(window).width();
    $(window).height();
    
    
    //增删改DOM元素
    //方法1(直接显示在页面上)
    $("元素").html("html标签内容");
    //方法2(通过下列方法追加)
    var dom1 = $("html标签内容");
    
    $(selector).after(content);//在匹配元素后面添加内容
    $(selector).append(content);//将content作为元素的内容插入到该元素内的最后
    $(selector).appendTo(content);//和append方向相反
    $(selector).before(content);//与after方法互换
    $(selector).prepend(content);//将content作为该元素的一部分，放到该元素的最前面
    $(selector).prependTo(content);//和prepend方向互换
    $(selector).insertAfter(content);//将该元素插入到content之后
    $(selector).insertBefore(content);//将该元素插入到content之前
    
    $(selector).remove();//删除所有的指定元素,自杀（本质上还是先找父再删自己）
    $(selector).remove("exp");//删除所有含有exp的元素
    $(selector).wrap("html");//用html来包围该元素
    $(selector).wrap(element);//用element来包围该元素
    $(selector).clone(布尔表达式);//当布尔表达式为真时，克隆元素（无参时，当作true处理）
    $(selector).empty();//将该元素的内容设置为空
    
    //克隆
    $(selector).clone(true/false);//返回元素的一份副本，参数代表是否把事件一起克隆，默认时false
    
    //offset & position
    //offset获取元素距离document的位置，返回值为对象[left:100,top:100]
    $(selector).offset();
    //position获取相对于其最近的有定位的祖先元素的距离
    $(selector).position();
    
    //scrollTop & scrollLeft
    //设置或者获取垂直滚动条的位置
    $(window).scrollTop();
    $(window).scrollLeft();
    //也可以带参数设置滚动条滚动的距离
</script>
```

## 动画

```javascript
	//显示 & 隐藏动画
	$(selector).show(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});
    $(selector).hide(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});
    $(selector).toggle(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});
    
    //其他动画
    $(selector).slideDown(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//下滑生成
    $(selector).slideUp(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//上滑消失
    $(selector).slideToggle(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//反复横跳
    $(selector).fadeIn(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//淡入
    $(selector).fadeOut(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//淡出
    $(selector).fadeTo(参数1:毫秒数/'fast'/'normal'/'slow',参数2:透明度,参数3:function(){动画执行完后的回调函数});//淡入到哪里
    $(selector).fadeToggle(参数1:毫秒数/'fast'/'normal'/'slow',参数2:function(){动画执行完后的回调函数});//反复横跳


	//自定义动画
	/*参数：
	参数1：需要做的动画属性
	参数2：执行动画的时长
	参数3：缓动swing or 匀速linear
	参数4：动画执行完毕后的回调函数
	*/
	$("元素").animate({
        left: 800,
        width: 200,
        height: 300,
        opacity: 0.5
    },2000,'linear',function(){
        alert('动画执行完毕了');
        //这里面又可以继续animate，无线套娃
    });

//因为都设置了动画的时间，所以存在一个动画队列，即使操作已经结束，但是动画可能并没有结束，导致交互很糟糕，需要在操作结束时stop动画
stop(true,true);//参数1：是否清除队列；参数2：是否跳转到最终效果
```

### 多库共存

```javascript
//查看jQuery版本
console.log(jQuery.fn.jquery);
console.log(jQuery.prototype.jquery);
console.log($.fn.jquery);
console.log($.prototype.jquery);

//引入多个jQuery文件，使用的是哪个版本的？
//哪个文件后引入就使用的那个版本的 $对象 和 jQuery对象 

//多库共存
$.noConflict();//主动把当前自己版本的$控制权释放掉
console.log(jQuery.fn.jquery);
console.log($.fn.jquery);
//这样上面就是两个版本的jQuery了，实现了多库共存
//同时noConflict返回原来的版本_$
var _$ = $.noConflict();
```

## 插件

jQuery插件搜索网站：`https://www.jq22.com/`

```javascript
//常用jQuery插件：颜色插件，省市联动插件，jQuery ui插件

/*自己开发jQuery插件
	1. 给jQuery原型添加方法
	2. 给jQuery直接添加方法
*/
(function($){
    //给jQuery原型添加方法(jquery.fn = jquery.prototype )
    $.fn.bgColor(bg_color){
        //this是调用这个bgColor方法的jQuery对象
        this.css("backgroundColor",bg_color);
        return this;//为了能链式编程
    }
}(jQuery))


(function($){
    //给jQuery原型添加方法(jquery.fn = jquery.prototype )
    $.bgColor(bg_color){
        //this是调用这个bgColor方法的jQuery对象
        this.css("backgroundColor",bg_color);
        return this;//为了能链式编程
    }
}(jQuery))
```



**jQuery支持链式编程**

原因：因为很多jQuery方法直接返回 当前的jQuery对象，所以可以接着 . 出方法；

1. 不返回jQuery对象的方法：val(),html(),text(),attr(), 返回文本字符串；

2. 返回新的jQuery对象的方法：next() nextAll() parent() children() prev() ...

3. 剩下基本都是返回当前jQuery对象；

```javascript
$("div").width(200px).height(100px).css("backgroundColor","red").text("哈哈");
```



**jQuery的很多操作通过实践中自己总结。**
