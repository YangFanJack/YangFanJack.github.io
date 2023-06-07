# markdown基础语法


# markdown基础——博客必备

## 推荐编辑器

1. typora
2. vscode

## 语法

### 1. 标题

markdown的标题一共分为六个级别，从大到小依次是：

~~~markdown
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题
~~~

### 2. 字体

* **加粗**

  ~~~markdown
  **这是加粗**
  ~~~

* **斜体**

  ~~~markdown
  *这是斜体*
  ~~~

* **斜体并加粗**

  ~~~markdown
  ***这是斜体并加粗***
  ~~~

* **删除线**

  ~~~mark
  ~~这是删除线~~
  ~~~

### 3. 引用

可嵌套

~~~markdown
> 引用1
>> 引用2
>>> 引用3
~~~

### 4. 分割线

需要三个或者三个以上的*或-

~~~markdown
**********
~~~

### 5. 图片

~~~markdown
![图片下面的文字解释](图片地址)
~~~

### 6. 超链接

~~~markdown
[名字](链接地址)
~~~

### 7. 列表

* **无序列表**

~~~markdown
* 无序列表1
+ 无序列表2
- 无序列表3
~~~

* **有序列表**

~~~ markdown
1. 有序列表1
2. 有序列表2
3. 有序列表3
~~~

* **嵌套列表**

上级列表和下级列表之间敲三个空格

~~~ markdown
1. 一级列表
   * 二级列表1
   * 二级列表2
2. 一级列表2
   * 二级列表3
   * 二级列表4
~~~

### 8. 表格

~~~markdown
a|a|a
--|:--:|--:
a|b|c
d|e|f
g|h|i
~~~

a|a|a
-|:-:|-:
a|b|c
d|e|f
g|h|i

### 9. 代码

* **单行代码**

~~~markdown
~ 单行代码 ~
~~~

* **多行代码**

~~~markdown
​~~~c++
~~~

### 10. 流程图

太复杂，推荐下载typora这个第三方工具，能更加方便

```markdowm
st=>start: Start
op=>operation: ifa<1
cond=>condition: Yes or No?
op1=>operation: 重新
e=>end
st->op->cond
cond(yes)->e
cond(no)->op1->e
```

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225226.png)


