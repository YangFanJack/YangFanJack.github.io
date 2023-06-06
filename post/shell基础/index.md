
* shell就是Linux中的命令解释器，说白了，就是一个命令行的交互界面。与Linux不同Windows中就是图形的交互界面。shell还是一个强大的编程语言，因为它可以直接调用Linux的系统命令。

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225445.png" style="zoom:50%;" />

* Shell的种类：
  * Bourne Shell语法类：sh，ksh，Bash，psh，zsh
  * C Shell语法类（只要同于BSD的Linux版本中）：csh，tcsh
  * 目前的主流shell时Bash Shell
* 在文件/etc/shells中，可以查看Linux中默认支持的Shell类型，在命令行下输入shell的名字就可以切换shell类型

# Shell脚本的执行方式

* echo命令：输出指定内容

  * echo -e：支持反斜线的字符转换 

* First Script：

  ```shell
  #!/bin/Bash
  echo -e "Mr Yang is the most handsome man in the world."
  ```

* 执行方法：
  * 第一种：先chmod 755  hello.sh         然后./hello.sh
  * 第二种：bash hello.sh
* 注意：如果一个shell脚本在Windows中编辑完成，在Linux中发现运行出错，这是因为Windows和Linux格式不同，你可以用`cat -A`来验证，换行符不用。解决方法就是用命令`dos2unix`将Windows格式转化为Linux格式

# Bash的基本功能

##     history

* history n：显示最近的n条命令
* history -d n：删除第n条命令
* history -c：清空历史命令
* history -w：把缓存中的历史命令写入每个用户的缓存文件~/.bash_history
* 可以使用上，下箭头的方式调用历史命令，也可以使用`!n`，`!!`，`!字串`直接执行指定的命令

##     TAB键

* 在Linux的bash中，Tab键可以用来不全命令和文件名

##     命令别名&常用快捷键

* 命令别名就是给命令定义一个别名，For Example：`alias vi='vim'`

* 直接用alias可以查看当前的所有别名，实际就是查看~/.bashrc文件的内容

* **命令执行的顺序：**

  1. 绝对路径或相对路径的命令
  2. 别名
  3. Bash的内部命令（内部命令就时用whereis找不到的Linux自带命令）
  4. 按找$PATH环境变量定义的目录查找顺序找到的第一的命令（外部命令）

* 让别名永久生效：写入~/.bashrc配置文件

* unalias：删除别名

* 常用快捷键：Ctrl+下列的字母

  c强制终止当前命令

  l清屏

  u剪切光标之前的内容

  k剪切光标后的内容

  y粘贴

  r搜索历史

  d退出当前终端，相当于logout]

  z暂停进程，放入后台

  s暂停屏幕输出

  q恢复屏幕输出

##     输入&输出重定向

* 标准输入输出设备

  键盘：/dev/stdin：标准输入：0文件描述符

  显示器：/dev/sdtout：标准输出：1文件描述符

  显示器：/dev/sdterr：标准错误输出：2文件描述符

* **输出重定向：**

  改变输出方向，把命令的正确或者输出结果输出到指定的文件中

* 正确输出和错误输出同时保存进一个文件中：

  以覆盖的方式：命令 > 文件 2>&1     或者      命令 &>文件

  以追加的方式：命令 >> 文件 2>&1     或者      命令 &>>文件

  把正确的输出保存进文件1，错误的输出保存进文件2：命令 >>文件1 2>>文件2

* **输入重定向：** wc，后面既可以直接加文件名，也可以将输入重定向作为输入，不过后者在结果中不会显示文件名，因为它指挥识别输入的文件内容流

  wc -c：统计字节数

  wc -w：统计单词数

  wc -l：统计行数

##     多命令顺序执行&管道符

* 多命令顺序执行

  * 分号：两个命令都会执行
  * &&：命令1正确执行，命令2才会执行
  * ||：命令1错误执行，命令2才会执行

* dd命令：磁盘复制命令，和cp命令不同，dd可以复制特殊文件，分区甚至整个硬盘。主要的作用就是磁盘复制

  dd if=输入文件 of=输出文件 bs=多少字节数作为一个块 count=块的个数

  `date;dd if=/dev/zero of=/root/testfile bs=1k count=100000;date`用来显示磁盘复制的时间

* `命令 && echo "yes" || echo "no"`：用来判断命令是否正确执行

* 管道符：命令1的**正确输出**作为命令2的操作对象

* grep命令：在文件中搜索符合条件的字符串

  grep -i：忽略大小写

  grep -n：输出行号

  grep -v：反向查找

  grep --color=auto：搜索出的关键字用颜色显示

  netstat -an | grep ESTABLISHED

##     通配符&其他特殊符号

* ?：匹配一个任意字符

* *：匹配任何内容（0个或任意多个字符）

* []：匹配中括号中的任意一个

* [-]：匹配中括号中范围内任意一个

* [^]：逻辑非，表示匹配任意一个不是中括号内的一个字符`[^0-9]`表示任意一个不是数字的字符

* **通配符是用来匹配文件名的，** 通配符通常会用来删除指定范围的文件

* **单引号** 中的所有符号都是符号，**双引号**中的符号可能会有特殊意义

  `echo "$SHELL"`和`echo '$SHELL'`

* **反引号** 和`$()`中的内容时系统命令

  `echo "$(ls)"`和`echo '$(ls)'`

* `#`开头时注释

* `$`用来调用变量

* `\`用来将特殊符号变成普通符号

# Bash的变量

##     用户自定义变量

* 变量名不能用数字开头

* bash中，变量的默认类型都是字符串型，如果要进行数值运算，需要指定变量类型为数值型

* 变量用单号连接，两侧不能空格，变量名若有空格，需要引号

* 变量值中可以用转义符\让特殊字符失去特殊含义

* 变量值可以进行叠加

* 环境变量名建议大写

* 变量分类：

  * 用户自定义变量
  * 环境变量
  * 位置参数变量
  * 预定义变量

* **用户自定义变量（本地变量）**

  name="jack"    可以叠加：`newname="$name"yang` 或者 `newname=${name}yang`

* set：查看系统中所有的变量

* unset 变量名：删除变量

##     环境变量

* 本地变量只在当前的shell中生效
* 环境变量会在当前和这个shell的所有子shell中生效，如果把环境变量写入相关的文件，那么这个环境变量会在所有的shell中生效
* 用`pstree`可以查看shell的父子关系

* `export 变量名`：申明普通变量为环境变量
* `env`：专门查看环境变量
* 系统环境变量：
  * PATH：查找系统命令的变量，tab键补全和外部命令的查找都是根据$PATH来的，可以用变量叠加的方式把自己的命令加到$PATH中，`PATH="$PATH":/root/test.sh`
  * PS1：定义系统提示符的变量，`echo $PS1`    然后用，`PS1=’格式‘`来自定义命令提示符

## 位置参数变量

## 位置参数变量

* $n：$0表示命令本身，之后就是命令行参数
* $*：所有参数，把所有参数当作一个整体
* $@：所有参数，把参数区别对待
* $#：所有参数的个数

## 预定义变量

* $?：返回上一次执行结果正确与否
* $$：当前进程的PID

* $!：后台运行的最后一个进程的PID

* 接受键盘输入：read [选项] [变量名]

  -p "提示信息"：

  -t 秒数：限定时间

  -n 字符数：限定字符数

  -s：隐藏输入的信息，适用于输入密码时

# Bash的运算

##         数值运算与运算符

* Linux中变量默认类型时字符串

* `declare [+/-][选项] 变量名`

  -：给变量设定类型属性，例子：-i：将变量声明为整数类型；-x：将变量声明为环境变量

  +：取消变量的类型属性

  -p：显示指定变量的被声明的类型
  
* **For Example:（四种计算格式，最常用的是第三种）**

  `declare -i cc=$aa+$bb`

  `cc=$(expr $aa + $bb)`

  `cc=$(($aa+$bb))`

  `cc=$[$aa+$bb]`

##         变量测试与内容替换

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225457.png" style="zoom: 80%;" />

# 环境变量配置文件

##     环境变量配置文件简介

* `source 配置文件`或者`. 配置文件`：让修改后的配置文件直接生效

* 环境变量配置文件中主要就是定义对系统的操作环境生效的系统默认环境变量，比如PATH，HISTSIZE，PS1，HOSTNAME等

* 主要的5个配置文件

  `/etc/profile`：针对所有用户

  `/etc/profile.d/*.sh`：针对所有用户

  `~/.bash_profile`：针对单个用户

  `~/.bashrc`：针对单个用户

  `/etc/bashrc`：针对所有用户

##     环境变量配置文件作用

* **环境变量配置文件调用的顺序：**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225504.png" style="zoom: 67%;" />

* `/etc/profile`：针对所有用户

  USER变量
  LOGNAME变量
  MAIL变量
  PATH变量
  HOSTNAME变量
  HISTNAME变量
  HISTSIZE变量
  umask
  调用/etc/profile.d/*.sh文件

* `/etc/profile.d/*.sh`：针对所有用户

  执行profile.d目录下所有sh文件

* `~/.bash_profile`：针对单个用户

  追加PATH：在PATH变量后面加上了`:$HOME/bin`这个目录

  调用~/.bashrc

* `~/.bashrc`：针对单个用户

  定义别名

* `/etc/bashrc`：针对所有用户

  定义别名和PS1

  会重复调用PATH，umask啥的，但是只针对no login shell的情况，就是直接敲sh进入一个shell的情况

##     其他配置文件和登录信息

* 注销时的配置文件：~/.bash_logout

* 历史命令的保存文件：~/.bash_history

* 登录信息：

  本地终端欢迎信息：/etc/issue

  远程终端欢迎信息：/etc/issue.net

  * 转义符在该文件中不能使用，只能纯文本登录
  * 是否生效由ssh的配置文件`/etc/ssh/sshd/config`决定，要加入`Banner /etc/issue.net`，重启ssh服务生效：`service sshd restart`

* 登陆后的欢迎信息：/etc/motd（本地和远程都适用）

  推荐特效字符定制网站[ASCII Generator](http://network-science.de/ascii/)

