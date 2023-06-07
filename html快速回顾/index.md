# HTML快速回顾


## 初识HTML

* Hyper Text Markup Language

* 超文本：文字，图片，音频，视频，动画等

* HTML5：

  * HTML5和CSS3是未来发展趋势

* W3C：World Wide Web Consortium万维网联盟，Web技术领域最权威和具影响力的**国际中立性技术标准机构**，W3C标准包括：

  * **结构化** 标准语言（HTML，XML）
  * **表现** 标准语言（CSS）
  * **行为** 标准（DOM，ECMAScript）

* HTML基本结构

  ```html
  <html>
      <head>
          <title>My Frist Page</title>
      </head>
      <body>
          My First Page
      </body>
  </html>
  ```

* <body>、</body>等成对的标签，分别叫**开放标签**和**闭合标签**

## 网页基本标签

```html
<!--DOCTYPE告诉浏览器使用什么规范-->
<!DOCTYPE html>
<html lang="en"><!--中文网站设置为zh-CN-->
<!--网页头部-->
<head>
    <!--描述网站一些信息，可用做seo-->
    <meta charset="UTF-8">
    <!--页面关键词，用于被搜索引擎收录-->
    <meta name="keywords" content="学习，记录">
    <!--页面描述，用于搜索引擎收录-->
    <meta name="excerpt" content="我的第一个网页">
    <!--用于控制页面缩放-->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!--网页标题-->
    <title>Document</title>
</head>
<!--网页主体-->
<body>
    
</body>
</html>
```

* 标题标签

  ```html
  <h1>一级标题</h1>
  <h2>二级标题</h2>
  <h3>三级标题</h3>
  <h4>四级标题</h4>
  <h5>五级标题</h5>
  <h6>六级标题</h6>
  ```

* 段落标签

  ```html
  <p>段落内容</p>
  ```

* 换行标签

  ```html
  <br/>
  <br>
  ```

* 水平线标签

  ```html
  <hr/>
  <hr>
  ```

* 字体样式标签

  ```html
  <strong>粗体</strong>
  <em>斜体</em>
  ```

* 注释和特殊符号

  ```html
  <!--注释-->
  &nbsp;空格
  &gt;大于
  &lt;小于
  &copy;版权符号
  <!--更多的符号可以百度-->
  ```

## 图像，超链接，网页布局

* 常见图像格式：JPG，GIF，PNG，BMP

  ```html
  <img src="path" alt="替代文字" title="鼠标悬停文字" width="x" height="y"/>
  ```

* 链接标签：

  ```html
  <a href="path" target="目标窗口位置">text/img</a>
  <!--
  target:
  	blank新标签网页中打开
  	parent父框架集中打开
  	self当前网页中打开（默认）
  	top在整个窗口中打开
  	framename指定的框架中打开
  -->
  
  <!--锚链接：通过name跳-->
  <a name="point"></a>
  <a href="#point"></a>
  <!--不同页面也可以锚链接-->
  <a href="xxx.html#point"></a>
  
  <!--邮件链接-->
  <a href="mailto:yangfanjack1024@qq.com">点击联系我</a>
  <!--QQ链接-->
  <a target="_blank" href="http://wpa.qq.com/msgrd?v=3&uin=你的qq号&site=qq&menu=yes"><img border="0" src="http://wpa.qq.com/pa?p=2:你的qq号:52" alt="点击这里给我发消息" title="点击这里给我发消息"/></a>
  ```

  ### 行内元素和块元素

  * 块元素：
    * 无论内容多少，该元素独占一行
    * p标签，h1-h6标签，div元素......
  * 行内元素：
    * 内容撑开宽度，左右都是行内元素的可以旁在一行
    * a元素，strong元素，em元素，span标签......

## 列表，表格，媒体元素

* **列表：**

  * 信息资源结构化，条理化地展示出来，方便浏览者快速获得相应信息
  * 分类：
    * 无序列表
    * 有序列表
    * 自定义列表

  ```html
  <!--有序列表-->
  <ol>
      <li></li>
      <li></li>
      <li></li>
      <li></li>
  </ol>
  <!--无序列表-->
  <ul>
      <li></li>
      <li></li>
      <li></li>
      <li></li>
  </ul>
  <!--自定义列表-->
  <dl>
      <dt>学科</dt>
      <dd>Java</dd>
      <dd>设计模式</dd>
      <dd>数据库</dd>
      
      <dt>城市</dt>
      <dd>成都</dd>
      <dd>北京</dd>
      <dd>上海</dd>
  </dl>
  ```

* **表格**

  * 简单通用，结构稳定
  * 基本结构：
    * 单元格
    * 行
    * 列
    * 跨行
    * 跨列

  ```html
  <table border="1px">
      <tr>
      	<th>姓名</th>
          <th>性别</th>
          <th>成绩</th>
      </tr>
      <tr>
      	<td>Mariah Carey</td>
          <td>female</td>
          <td>100</td>
      </tr>
      <tr>
      	<td>Sam Smith</td>
          <td>male</td>
          <td>99</td>
      </tr>
  </table>
  
  <!--跨行 or 跨列-->
  <table border="1px">
      <tr>
      	<td colspan="4">歌手信息</td>
      </tr>
      <tr>
          <td rowspan="2">欧美歌手</td>
      	<td>Mariah Carey</td>
          <td>female</td>
          <td>100</td>
      </tr>
      <tr>
      	<td>Sam Smith</td>
          <td>male</td>
          <td>99</td>
      </tr>
  </table>
  ```

* 视频 & 音频

  video and audio

  ```html
  <video src="视频资源路径" controls autoplay></video>
  <audio src="音频资源路径" controls autoplay></audio>
  ```

### 页面结构分析

* **header**：标题头部区域内容
* **footer**：标记脚步区域内容
* **section**：Web页面中的一块独立区域
* **article**：独立的文章内容
* **aside**：相关内容或应用
* **nav**：导航类辅助内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <header>网页头部</header>
    <section>
    	网页主体
    </section>
    <footer>网页脚部</footer>
</body>
</html>
```

### iframe内联框架 & 框架集

```html
<iframe src="path" name="框架标识名" frameboder="0" width="1000px" height="800px"></iframe>

<a href="path" target="框架标识名">跳转到内联框架</a>

<!--iframe对seo不友好，一般不建议使用-->
<!--frameset可以使用，能用来做网页布局-->
<frameset cols="25%,50%,25%">
  	<frame src="frame_a.htm" />
  	<frame src="frame_b.htm" />
  	<frame src="frame_c.htm" />
</frameset>
```

## 表单及表单应用

```html
<!--
action：表单提交的位置，可以是网站，也可以是一个请求处理地址
method：
	get方式提交：url中可以看见我们提交的信息，高效但不安全，只能传输少量数据
	post方式提交：比较安全，可以传输大量数据
-->
<form action="path" method="get/post">
    <!--输入类-->
    <input type="text" value="userName" name="text" maxlength="10" />
    <input type="password" value="password" name = "password" maxlength="10" />
    <input type="hidden" value="id" name="id"/><br/>

    <!--选择类-->
    <input type="radio" name = "sex"  value = "1"/></br>
    <input type="checkbox" name="fav" checked="false" value="1"/>

    <!--文件类-->
    <input type="file" name = "file" multiple accept="image/gif, image/jpeg"/>

    <!--按钮类-->
    <input type="button" value="纯按钮" />
    <input type="submit" value = "提交" />
    <input type="reset" value = "重置" />
    <input type="image" src="图片资源路径"/>

    <button type="submit">提交</button>
    <button type="reset">重置</button>
    <button type="button">无行为</button>
    <button>默认是submit行为</button>

    <!--下拉框-->
    <select name="city">
        <option value="1">北京</option>
        <option value="2" selected>上海</option>
    </select>

    <!--文本域标签-->
    <textarea name = "introduce" maxlength="500" cols="20" rows="10"></textarea>

    <!--label：输入标签的文字描述标签-->
    <label for="sex1">女</label>
    <input id = "sex1" type="radio" name = "sex"  value = "1"/>

    <!--带验证的标签-->
    <input type="email" name="email"/>
    <input type="url" name="url"/>
    <input type="number" name="number" max="100" min="0" step="10"/>

    <!--滑块-->
    <input type="range" min="0" max="100" name="voice" step="2"/>

    <!--搜索-->
    <input type="search" name="searchString"/>
</form>
```

* **表单的限制（三个属性）：**
  * 隐藏域：`hidden`
  * 只读：`readonly`
  * 禁用：`disabled`

## 表单初级验证

* 为啥要进行表单验证？
  * 保证数据安全性
  * 减轻服务器压力
* **初级验证（三个属性）：**
  * `placeholder`：提示信息
  * `required`：非空判断
  * `pattern`：正则表达式判断
