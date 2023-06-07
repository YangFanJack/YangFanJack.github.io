# CSS快速回顾


* **前端三件套**

HTML+CSS+JavaScript

结构+表现+交互

* css历史
  * 1.0
  * 2.0
  * 2.1
  * 3.0
* 怎么学CSS
  1. CSS是什么
  2. CSS怎么用
  3. CSS选择器（重难点）
  4. 美化网页（文字，阴影，超链接，列表，渐变）
  5. 盒子模型（重点）
  6. 浮动
  7. 定位
  8. 网页动画（特效）

## 什么是CSS

* 层叠样式表(Cascading Style Sheets)，美化网页（字体，颜色，边距，高度，宽度，背景图片，网页）
* 发展史：
  * CSS1.0：高耦合内联基础样式
  * CSS2.0：DIV+CSS，HTML和CSS结构分离的思想，网页变得简单，利于SEO
  * CSS2.1：浮动，定位
  * CSS3.0：圆角，阴影，动画.....有浏览器兼容性问题（IE6前端之殇）

## CSS快速入门

```css
/*
规范语法：
选择器{
	属性1:属性值;
	属性2:属性值;
	...
}
*/
h1{
    color: red;
}
```

* CSS优势：
  * 内容和表现分离
  * 网页结构表现统一，可以实现复用
  * 样式十分丰富
  * 建议使用独立于html的css文件
  * 利用seo，容易被搜索引擎收录

### 三种导入方式

```html
<!--1. 行内样式-->
<h1 style="color: red">中华人民共和国</h1>

<!--2. 内部样式表-->
<!DOCTYPE>
<html>
<head>
  <meta charset="utf-8" />
  <title>内部样式表</title>
  <!--使用内部样式表引入CSS-->
  <style type="text/css">
    div{
        background: green;
    }
  </style>
</head>
<body>
     <div>我是DIV</div>
</body>
</html>

<!--3. 外部样式表-->
<!DOCTYPE>
<html>
<head>
  <meta charset="utf-8" />
  <title>外部样式表</title>
  <!--链接式:推荐使用-->
  <link rel="stylesheet" type="text/css" href="css/style.css" /> 
  <!--导入式:CSS2.1特有的-->
  <style type="text/css">
    @import url("css/style.css");
  </style>
</head>
<body>
     <ol>
         <li>1111</li>
         <li>2222</li>
     </ol>
</html>

```

三种样式表的**优先级**：**就近原则**

## 选择器

**作用：选择页面上的某一个或者某一类元素**

### 标签选择器

```html
<!--会选择页面上所有的这个标签的元素-->
<style>
	h1{
    	color: red;
	}
</style>

<h1>The People's Republic Of China</h1>
```

### 类选择器

```html
<!--会选择页面上所有该类的元素，即可以复用-->
<style>
	.redClass{
    	color: red;
	}
</style>

<h1 class="redClass">The People's Republic Of China</h1>
<h1 class="redClass">中华人民共和国</h1>
```

### ID选择器

```html
<!--会选择页面上所有该ID的元素,ID全局唯一-->
<style>
	#buleClass{
    	color: blue;
	}
	#redClass{
    	color: red;
	}
</style>

<h1 id="blueClass">美利坚合众国</h1>
<h1 id="redClass">中华人民共和国</h1>
```

**三种基础选择器的优先级：**ID选择器 > Class选择器 > 标签选择器

### 层次选择器

```html
<!--
1. 后代选择器：在某个元素里面的所有
2. 子选择器：后一代，子女
3. 相邻兄弟选择器：同父母的后面那个兄弟元素
4. 通用选择器：当前选中元素的向下所有兄弟元素
-->
<style>
    /*后代选择器*/
    body p{
        background: green;
    }
    /*子选择器*/
    body>p{
        background: red;
    }
    /*相邻兄弟选择器*/
    .choosed+p{
        background: blue;
    }
    /*通用选择器*/
    .choosed~p{
        background: grey;
    }
</style>

<p>p1</p>
<p class="choosed">p2</p>
<p>p3</p>
<ul>
    <li>
    	<p>p4</p>
    </li>
    <li>
    	<p>p5</p>
    </li>
    <li>
    	<p>p6</p>
    </li>
</ul>
```

### 伪类选择器 & 伪元素选择器

```html
<!--
伪类选择器
是用来选择某个类，或者说，选择的是某个类的状态。
-->
<style>
	/*ul的第一个子元素*/
    ul li:first-child{
        color: red;
    }
    /*ul的最后一个子元素*/
    ul li:last-child{
        color: blue;
    }
    /*只选中p1*/
    p:nth-child(1){
        /*选中当前元素的父级的第一个子元素，如果在p1前加一个非p的标签则选不上p1*/
        background: red;
    }
    p:nth-of-type(2){
        /*选择当前元素中的父级的第一个和当前元素同类型的子元素*/
        color: red;
    }
</style>

<p>p1</p>
<p>p2</p>
<p>p3</p>
<ul>
    <li>li1</li>
    <li>li2</li>
    <li>li3</li>
</ul>
```

```css
/*a标签伪类选择器*/
/*鼠标未点击*/
a:link    {color:blue;}
/*鼠标点击后*/
a:visited {color:blue;}
/*鼠标悬浮*/
a:hover   {color:red;}
/*鼠标摁住未释放*/
a:active  {color:yellow;}
```

```css
/*
伪元素选择器
是用来在文档中插入假象的元素。CSS2.1的写法。
*/

/*首字母和首行*/
:first-letter
:first-line

/*之前和之后*/
:before
:after
```

### 属性选择器

最好用的一种选择器：相当于把class选择器和id选择器结合了

```html
<style>
    .demo a{
        float: left;
        display: block;
        height: 50px;
        width: 50px;
        border-radius: 10px;
        background: green;
        text-align: center;
        color: red;
        text-decoration: none;
        margin-right: 5px;
        font: bold 20px/50px Arial;
    }
    /*存在id属性的元素:属性名   [属性名=属性值(可使用正则)]*/
    a[id]{
        background: yellow;
    }
    a[id=first]{
        background: gray;
    }
    a[class*="links"]{
        /*
        =:绝对等于
        *=:包含
        ^=:以...开头
        $=:以...结尾
        */
        background: pink;
    }
</style>

<p class="demo">
    <a href="http://www.baidu.com" class="links item first" id="frist">1</a>
    <a href="#" class="links item active" id="second" target="_blank" title="test">2</a>
    <a href="images/123.html" class="links item" id="third">3</a>
    <a href="images/123.png" class="links item" id="fourth">4</a>
    <a href="images/123.jpg" class="links item" id="fifth">5</a>
    <a href="abc" class="links item" id="sixth">6</a>
    <a href="/a.pdf" class="links item" id="seventh">7</a>
    <a href="abc.pdf" class="links item" id="eighth">8</a>
    <a href="abc.doc" class="links item" id="ninth">9</a>
    <a href="abcd.doc" class="links item last" id="tenth">10</a>
</p>
```

## 字体 & 文本 等 美化

1. 有效传递页面信息
2. 美化网页，页面漂亮能吸引用户
3. 凸显网页主题

```html
<span>重点要突出的字用span标签套起来</span>
欢迎学习<span>Java</span>
```

### 字体样式

```html
<style>
    body{
        /*字体*/
        font-family: 楷体;
        /*颜色*/
        color: red;
    }
    h1{
        /*大小*/
        font-size: 50px;
    }
    p{
        /*粗细*/
        font-weight: bold;
        /*集合font*/
        font: font-style || font-variant || font-weight || font-size || line-height || font-family
    }
        
</style>

<body>
    <h1>故事介绍</h1>
    <p>
    尘世之锁是《原神》中的一件五星法器，只能通过祈愿获得。尘世之锁原属于尘之魔神归终，归终具有超越众多仙神的智慧，她总是与岩之神摩拉克斯一同行动，想要同摩拉克斯一起建设璃月。而最终她未能和摩拉克斯一同见证璃月的安宁与繁华。尘世之锁作为盟约的信物，一直留在岩之神的手中。
    </p>
	<p>
    这是一个表面被71%的海水覆盖着的蓝色星球，人类在这片碧蓝色之中出生、成长、孕育和发展属于自己的文明。然而，伴随着它们的，还有不断膨胀的野心和欲望。表面风平浪静的世界格局之下，历史的暗流涌动。终于，未知的敌人在海洋中出现，面对敌方压倒性的战力，各个阵营却仍然各自为战，最终换来的结果亦是惨痛的：人类失去了90%以上的海域控制权，科技和生活水平急速倒退。人类对于海洋以及未知的敌人产生了深深的阴影，并且将那些将他们拖入深海黑暗之中的怪物称之为『塞壬』 。数十年之后，各大阵营为了夺回曾经的辉煌，终于摒弃前嫌，联合创建了第三方军事组织『碧蓝航线』 。碧蓝航线的宗旨在于：集结并共享来自来自世界各大阵营的科技与资源，建造出能够与塞壬抗衡的新锐部队，夺回海域的控制权。
	</p>
</body>

```

### 文本样式

1. 颜色

   ```css
   h1{
       color: red;
       color: #FF0000;
       color: rgb(255,0,0,0.4);/*最后是浓度*/
   }
   ```

2. 文本对齐方式

   ```css
   p{
       text-align: center;
       vertical-align: middle;
   }
   ```

3. 首行缩进

   ```css
   p{
       text-indent: 2em;
   }
   ```

4. 行高

   ```css
   p{
       line-height: 10px;/*和height一致就可以单行文本垂直居中*/
   }
   ```

5. 装饰

   ```css
   p{
       text-decoration: underline;/*下划线*/
       text-decoration: overline;
       text-decoration: line-through;
       text-decoration: none;
   }
   ```

6. 阴影

   ```css
   h1{
       /*text-shadow: h-shadow v-shadow blur color*/
       text-shadow: 5px 5px 5px #FF0000;
   }
   ```

### 其他样式

1. 列表美化

   ```css
   ul li{
       height: 30px;
       /*
       可以按顺序设置如下属性：
   		list-style-type:none,disc,circle,square,decimal...
   		list-style-position
   		list-style-image
       */
       list-style: square inside url('/i/arrow.gif'); 
   }
   ```

2. 背景 & 渐变

   ```css
   div{
       /*
       可以设置如下属性：
       	背景颜色background-color
   		背景图像位置background-position
   		背景图片尺寸background-size
   		如何重复background-repeat
   		规定背景图片的定位区域background-origin
   		规定背景图片的绘制区域background-clip
       	图像是否固定or随着页面滚动background-attachment
   		背景图片地址background-attachment
       */
       background: #00FF00 url(bgimage.gif) no-repeat fixed top;
   }
   ```

## 盒子模型

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210109165022.gif" style="zoom:80%;" />

1. **border：**

   ```html
   <style>
       /*body默认有一个8px的margin,一般先干掉他*/
       /*一般开发的时候会专门一个reset.css文件*/
       body{
           margin: 0;
       }
       /*border: 粗细,样式,颜色*/
   	#app{
   		width: 300px;
   		border: 1px solid red;
   	}
   </style>
   
   
   <div id="app">
       
   </div>
   ```

2. **margin & padding：**

   ```html
   <style>
   	/*auto表示自动，"margin： 20px auto；"就会使浏览器根据窗口大小自适应地使定宽块状元素左右居中显示*/
       #app{
           /*顺序是上右下左*/
           margin:10px 5px 15px 20px;
           /*两个是上下，左右*/
           padding:10px 10px;
           /*三个是上，下，左右*/
           padding:10px 5px 10px;
       }
   </style>
   
   <div id="app">
       
   </div>
   ```

3. **圆角边框 & 阴影**

   ```html
   <style>
       #app{
           width:100px;
           height:100px;
           border:10px solid red;
           /*四个值：左上角、右上角、右下角、左下角
           两个值：左上右下对角线，右上左下对角线*/
           border-radius: 50px 20px;
           /*
           向 div 元素添加阴影：
           box-shadow: h-shadow v-shadow blur spread color inset;
           */
           box-shadow: 10px 10px 5px #888888;
       }
</style>
   
   <div id="app">
       
   </div>
   ```
   

注意：**margin和text-align的区别：**

1. text-align:center 设置文本或img标签等一些内联对象（或与之类似的元素）的居中。

2. margin:0 auto 设置块元素（或与之类似的元素）的居中。

   margin:0 auto；在不同场景下**生效条件**如下：

   ​    **块级元素：**给定要居中的块级元素的宽度。

   ​    **行内元素：**①设置display:block；②给定要居中的行内元素的宽度。（行内元素设置成块级元素后可以对其宽高进行设置）

   ​    **行内块元素：**设置display:block。（如input、button、img等元素，自带宽度可以不用设置其宽度）

## 文档流

### 元素分类

#### 块级元素（block-level）

块元素会独自占据一整行，或者多行，可以任意设置其大小尺寸，是用于搭建网页布局的必须部分，使网页结构更加紧凑合理。

* 总是另起一行（特立独行）
* 可以设置其宽度、高度，内外边距
* 在不手动设置宽度的情况下，宽度默认为所在容器的100%（即容器宽度）
* 可以容纳行内元素和其他块元素。

```html
常见的块级元素有：<div>/<h1>~<h6>/<p>/<ul>/<table>等，其中<div>是最常用最典型的块级元素。
```

#### 行内元素（inline-level）

行内元素也称为内联元素，行内元素不占有独立区域，其大小仅仅被动的依赖于自身内容的大小（例如文字和图片），所以一般不能随意设置其宽高、对齐等属性。常用于控制页面中文本的样式。

* 总是和相邻的行内元素在同一行上（物以类聚）
* 设置宽高无效，水平方向的padding和margin属性可以设置，但是垂直方向上的无效。
* 默认宽度是他自身内容的宽度。
* 行内元素只能容纳其他行内元素或者文本。

```html
<a>比较特殊，可以放块级元素，但是链接里面不能再放链接。
```

#### 行内块元素（inline-block）

普遍的规则里总有那么几个不一样的，在行内元素中就有那么几个特殊标签，比如`<img>/<input>/<td>`,可以给他们设置宽高、对齐属性，我们把这样特殊的一类标签称为行内块元素。行内块元素综合了块元素和行内元素的不同特点。

* 和相邻行内元素在同一行，但是之间会有空白缝隙。
* 默认宽度是他本身内容的宽度。
* 宽度、高度、行高、外边距以及内边距都可以手动设置。

#### 标签显示模式转换：displey

* 块元素——>行内元素 ： display:inline;
* 行内元素——>块： display:block;
* 块、行内元素——>行内块： display: inline-block;
* 元素不会显示：display:none;

### 文档流

* **文档流：**

  将窗体自上而下分成一行一行，众多盒子模型在每行中按从左至右依次排放元素，称为文档流。

* **脱离文档流：**

  元素脱离文档流之后，将不再在文档流中占据空间，而是处于浮动状态。当一个元素脱离文档流后，依然在文档流中的其他元素将忽略该元素并填补其原先的空间。

* **脱离文档流的三种方法：**

  * float:left/right;

    使用float脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在该元素的周围。

  * position: absolute;

    因为使用absolute脱离文档流后的元素，是相对于该元素的父类（及以上，如果直系父类元素不满足条件则继续向上查询）元素进行定位的，

    并且这个父类元素的position必须是非static定位的（static是默认定位方式）。

  * position: fixed;

    完全脱离文档流，相对于浏览器窗口进行定位。（相对于浏览器窗口就是相对于html）。

* **浮动：**

  ```css
  <html>
  
  <head>
  <style type="text/css">
  #a
  {
  	width:100px;
  	height:100px;
  	background: blue;
      float:left;
  }
  #b{
  	width:150px;
  	height:150px;
  	background: red; 
      float:left;
      /*清除其他浮动元素对自己的影响：left针对左浮动，right针对右浮动，both同时针对*/
      clear:left;
  }
  </style>
  </head>
  
  <body>
  <div id="a"></div>
  <div id="b"></div>
  </body>
  
  </html>
  ```

### 父级边框塌陷问题

**子元素浮动后脱离文档流导致的父级边框塌陷问题的解决方法：**

1. 增加父级元素的高度

2. 父元素中增加一个空div标签，然后清除浮动

   ```html
   <div class="clear"></div>
   <style>
       .clear{
           clear:both;
           margin:0;
           padding:0;
       }
   </style>
   ```

3. 开启BFC

   开启元素的BFC后，元素将会有以下特性：

   1. 垂直外边距**不会和子元素重叠**
   2. 不会被浮动元素所覆盖
   3. **可以包含浮动的子元素**

   如何开启BFC：

   1. 设置元素浮动
   2. 设置元素绝对定位
   3. 设置元素为inline-block
   4. 将overflow设置为一个非visible的值（推荐：将overflow设置为hidden是副作用最小的开启BFC的方法）

4. 父类添加一个伪类：

   ```css
   /*解决父子元素垂直外边距重叠问题*/
   .clearfix:before{
       content: "";
       display: table;
   }
   
   /*解决父元素高度塌陷问题*/
   .clearfix:after{
       content: "";
       display: block;
       clear: both;
   }
   
   /*
   同时解决父子元素垂直外边距重叠问题
   和父级高度塌陷问题
   */
   .clearfix:before,
   .clearfix:after{
   	content: "";
       display: table;
       clear: both;
   }
   ```

## 定位

### 相对定位

相对于**自己原来的位置**进行偏移，原来的位置还是被保留，元素仍然处于文档流中。

```css
div{
    background: red;
    position: relative;
    /*上下左右：相对自己原来位置*/
    top: 5px;
    bottom: 5px;
    left: 5px;
    right: 5px;  
}
```

### 绝对定位

绝对定位的元素的位置相对于**最近的已定位祖先元素**，如果元素没有已定位的祖先元素，那么它的位置相对于**最初的包含块**。

只能在相对的那个祖先元素**范围内**移动，元素绝对定位后已经脱离文档流

```css

div{
    background: red;
    position: absolute;
    /*上下左右：相对最近已定位祖先元素*/
    top: 5px;
    bottom: 5px;
    left: 5px;
    right: 5px; 
}
```

### 固定定位

相对于浏览器窗口进行定位，脱离原来的文档流

```css
div{
    background: red;
    position: fixed;
    /*上下左右:相对浏览器窗口*/
    top: 5px;
    bottom: 5px;
    left: 5px;
    right: 5px; 
}
```

### Z-index & 透明度

```css
div{
    /*设置图层显示优先级，范围0-999
    前提是必须先开启定位，即postion不能是static
    父元素层级再高也不会盖过子元素
    */
    position: relative;
    z-index: 1;
    
    /*两种方式设置透明度*/
    opacity: 0.5;
    /*IE8以及更早版本支持*/
    filter: Alpha(opacity=50);
}
```

## 动画

css3动画主要常用的属性有三种

1. 变形（transform）
2. 转换（transition）
3. 动画（animation）

```css
/*animation栗子*/
div{
	width:300px;
	height:200px;
	background:red;
	animation:mymove 5s infinite;
}
/*背景颜色逐渐地从红色变化到蓝色：*/
@keyframes mymove{
    from {background-color:red;}
    to {background-color:blue;}
}
```

## hack

CSS hack是通过在CSS样式中加入一些特殊的符号，让不同的浏览器识别不同的符号（什么样的浏览器识别什么样的符号是有标准的，CSS hack就是让你记住这个标准），**解决兼容性问题**。

1. 条件hack

```html
<!--[if IE 6]-->
	<p>只在ie6中显示的内容</p>
<!--[endid]-->

<!--[if lt IE 9]-->
	<p>只在小于ie9中显示的内容</p>
<!--[endid]-->
```

2. 属性hack

```css
div{
    /*属性前加_只有ie6及以下才认识*/
    _background-color: red;
    /*属性前加*只有ie7及以下才认识*/
    *background-color: red;
}
```

还有很多hack，有需求的时候百度就行了。

但是css的hack不到万不得已最好不要使用，因为维护起来很麻烦。
