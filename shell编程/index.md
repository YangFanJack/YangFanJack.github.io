# shell编程


# 基础正则表达式

* **正则表达式** 和 **通配符** 区别：

  通配符：在系统中搜索匹配文件名，是完全匹配。支持命令ls，find，cp，他们不认识正则表达式

  正则表达式：用来在文件中匹配符合条件的字符串，是包含匹配。支持命令：grep，awk，sed<!--more-->

* `*`：前一个字符匹配0次或者任意多次
* `+`：前一个字符匹配1次或者任意多次
* `?`：前一个字符匹配0次或者1次

* `.`：匹配任意一个字符（换行符除外）

* `^`：匹配以后面字符作为行首的行

* `$`：匹配以前面字符作为行尾的行

* `^$`：匹配空白行

* `()`：子表达式，子表达式可以获取供以后使用：特别是在替换字符串时，用$+数字可以代替子表达式
* `[]`：匹配中括号中的指定的任意一个字符
  * \w：字母、数字、下划线
  * \s：空白符，换行符
  * \S：非空白符，非换行符
  * \t：制表符
  * \t：回车符
  * \n：换行符
  * \b：单词边界

* `[^]`：匹配除中括号中的字符外的任意一个字符

* `\`：转义符

* `{n}`：表示其前面的字符恰好出现n次

* `{n,}`：表示其前面的字符出现不少于n次

* `{n,m}`：表示其前面的字符至少出现n次，最多出现m次
## 正则表达式的组匹配
* 正则表达式的小括号——子表达式 表示分组匹配
* 例如：尾行注释转行上注释
  * 匹配 ( *)(.+;)( *// *)(.+),替换为 $1// $4\n$1$2，
  * $1-$4分别对应上述四个分组匹配到的字符串,在替换中使用这些分组引用来构建新的字符串

# 字符截取命令

* ## **`grep`命令**：提取符合条件的行

  -c：只输出匹配行的计数

  -i：不区分大小写

  -v：显示不包含匹配文本的所有行

* ## **`cut`命令**：提取符合条件的列

  -f 列号：提取第几列

  -d 分隔符：按照指定分隔符分割列，默认是制表符tab

  `cut /etc/passwd | grep /bin/bash | grep -v root | cut -d ":" -f 1`：用来提取出普通用户名

  `df -h | grep "sda5" | cut -f 5`：用来提取硬盘的使用率

* ## **`printf`命令**：按找类型输出格式输出内容

  `%ns`：输出字符串

  `%ni`：输出整数

  `%m.nf`：输出浮点数

  \n，\r，\t：换行，回车，tab键

  `printf '%s %s %s' 1 2 3 4 5 6`：最后输出结果按照%s %s %s格式分为两组

  printf命令不能用管道符，只能`printf %s $(cat XXX.txt)`

  print命令：和printf语法一样，只是会默认换行，print不是系统命令，只能在awk中执行

* ## **`awk`命令**：截取列

  很强大的命令，可以说是一门编程语言

  **格式**：`awk ’条件1{动作1} 条件2{动作2} 条件3{动作3}‘ 文件名`

  * awk '{printf $2 "\t" $6 "\n"}' XXX.txt
  * `df -h | awk '{printf $1 "\t" $5 "\t" $6}'`：可以处理空格，弥补了cut的不足，但是awk很多命令很复杂
  * `df -h | grep sda5 | awk ’{print $5}‘ | cut -d "% -f 1"`

  **BEGIN**：在所有命令执行之前先执行BEGIN后面的语句块,awk默认是先读入一行再执行后面的语句

  **END**：在所有语句处理完后执行

  **FS**：指定分隔符，`awk ’{FS=":"}‘`

  **awk还支持条件判断**：`awk ’$6>=87 {printf $2 "\n"}‘`：

* ## **`sed`命令**：数据的流编辑器

  vim只能修改文件，sed还可以直接修改**管道符**传过来的流

  **格式**：sed [选项] '[动作]' 文件名

  选项：

  * -n：sed默认把所有数据都输出到屏幕，加上-n表示**只把经过sed修改过后的行输出到屏幕**

  * -e：允许对输入数据用**多条sed命令**编辑

  * -i：用sed的修改结果**直接修改读取数据的文件**，而不是由屏幕输出

  动作：

  * a：行后追加（多行时，行尾要加\）sed '2a hello' XXX.txt
  * c：替换（多行时，行尾要加\）sed '4c no the line' XXX.txt
  * i：行前插入（多行时，行尾要加\）sed '2i hello \ world' XXX.txt
  * d：删除：sed '2,4d' XXX.txt
  * p：打印：sed '2p' XXX.txt
  * s：字串替换：sed '4s old/new/g' XXX.txt

# 字符处理命令

* ## sort：排序

  * -r：反向排序
  * -t -k：指定排序标准：-t ":" -k 3,4：以:为分隔符，按找第3到第4列来排序
  * -n：按照数值大小来排

* ## wc：统计字符

  * -l：行数
  * -c：单词数
  * -w：字符数

# 条件判断

* ## **按照文件类型判断：**

  * -e：判断文件是否存在

  * -b：判断文件是否存在，并且是否是**块设备**文件

  * -c：判断文件是否存在，并且是否是**字符设备**文件

  * -d：判断文件是否存在，并且是否是**目录**文件

  * -f：判断文件是否存在，并且是否是**普通**文件

  * -L：判断文件是否存在，并且是否是**链接**文件

  * -p：判断文件是否存在，并且是否是**管道**文件

  * -S：判断文件是否存在，并且是否是**套接字**文件

  * -s：判断文件是否存在，并且是否是**非空**

  * **两种判断格式：**

    * test -e XXX.txt

    * [ -e XXX.txt ]：注意首尾各有一个空格
    * FOR EXAMPLE：`[-d /root] && echo "yes" || echo "no"`：如果是目录yes，否则no

* ## 按照文件权限进行判断

  * -r：判断文件是否存在，并且是否该文件有**读**权限，u，g，o中任意一个有都为真
  * -w：判断文件是否存在，并且是否该文件有**写**权限，u，g，o中任意一个有都为真
  * -x：判断文件是否存在，并且是否该文件有**执行**权限，u，g，o中任意一个有都为真
  * -u：判断文件是否存在，并且是否该文件有**SUID**权限，u，g，o中任意一个有都为真
  * -g：判断文件是否存在，并且是否该文件有**SGID**权限，u，g，o中任意一个有都为真
  * -k：判断文件是否存在，并且是否该文件有**SBIT**权限，u，g，o中任意一个有都为真

* ## 两个文件之间进行比较

  * 文件1 -nt 文件2：判断文件1的**修改时间**是否比文件2**新**
  * 文件1 -ot 文件2：判断文件1的**修改时间**是否比文件2**旧**
  * 文件1 -ef 文件2：判断文件1的**inode号**是否和文件2**一致**，可以用来判断两个文件是不是互为硬链接

* ## 两个整数之间比较

  * 整数1 -eq 整数2：相等
  * 整数1 -ne 整数2：不等
  * 整数1 -gt 整数2：大于
  * 整数1 -lt 整数2：小于
  * 整数1 -ge 整数2：大于等于
  * 整数1 -le 整数2：小于等于

* ## 字符串的判断

  * -z：判断是否为空
  * -n：判断是否为非空
  * 字串1 == 字串2：判断是否相等
  * 字串1 !=字串2：判断是否不等

* ## 多重条件判断

  * 判断1 -a 判断2：逻辑与
  * 判断1 -o 判断2：逻辑或
  * !判断：逻辑非

# 流程判断

* ## if语句：

  * 和`[-d /root] && echo "yes" || echo "no"`作用一样，但更直观

  * **单分支if**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225348.png" style="zoom:50%;" />

  * FOR EXAMPLE:

    ```shell
    #!/bin/bash
    rate=$(df -h | grep /dev/sda5 | awk '{print $5}' | cut -d "%" -f 1)
    if [ $rate -ge 80 ]
    	then
    		echo "Warning /dev/sda5 is coming full !!!"
    fi
    ```

  * **多分支判断：**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225355.png" style="zoom:50%;" />

* ## case语句：

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225406.png" style="zoom:50%;" />

* ## for循环：

  * **第一种语法：**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225413.png" style="zoom: 50%;" />

    FOR EXAMPLE：

    ```shell
    for i in 1 2 3 4 5 6
    	do
    		echo $i
    	done
    ```

    

  * **第二种语法：**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225420.png" style="zoom:50%;" />

    FOR EXAMPLE：

    ```shell
    !/bin/bash
    s=0
    for ((i=1;i<=100;i=i+1))
    	do
    		s=$(($s+$i))
    	done
    echo "The sum of 1+2+...+100 is $s"
    ```

* ## while循环和until编程：

  * **while循环：**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225427.png" style="zoom:50%;" />

    FOR EXAMPLE：

    ```shell
    !/bin/bash
    i=1
    s=0
    while [ $i -le 100 ]
    	do
    		s=$(( $s+$i ))
    		i=$(( $i+1 ))
    	done
    echo "The sum is: $s"
    ```

  * **until循环：**

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225435.png" style="zoom:50%;" />

    FOR EXAMPLE：

    ```shell
    !/bin/bash
    i=1
    s=0
    until [ $i -gt 100 ]
    	do
    		s=$(( $s+$i ))
    		i=$(( $i+1 ))
    	done
    echo "The sum is: $s"
    ```

    
