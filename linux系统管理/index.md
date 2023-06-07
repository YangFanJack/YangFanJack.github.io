# linux系统管理


**进程管理的作用：（下面优先级由高到低）**

1. 判断服务器的健康状态
2. 查看系统中的所有进程
3. 杀死进程

##     进程查看

* `ps aux`：查看系统中所有进程，查看BSD操作系统格式

* `ps -le`：查看系统中所有进程，Linux格式

* 输出格式的作用：

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224651.png" style="zoom: 50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224659.png" style="zoom:50%;" />

* `top`（每3秒更新一次）

  -d 秒数：默认每3秒更新一次，可指定

  ？或h：显示交互模式的帮助

  P：以CPU使用率排序

  M：以内存使用率排序

  N：以PID排序

  q：退出top

  **top命令的显示：**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224746.png)

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224706.png" style="zoom:50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224753.png" style="zoom:50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224809.png" style="zoom:50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224823.png" style="zoom:50%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224937.png" style="zoom:50%;" />

* `pstree`：查看进程树

##     进程终止

* `kill`

  -l：查看kill支持的信号

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225035.png" style="zoom:50%;" />

  -1 PID：重启进程

  -9 PID：终止进程

* `killall 进程名`：按照进程名杀死，选项和kill通用

  -i：有询问

  -I：忽略进程名的大小写

* `pkill 进程名`：按照进程名杀死，选项和kill通用

  也可以加t选项跟终端号：`pkill -t 终端号`：按照终端号踢出用户，用 `w` 命令查询系统中登录的用户，然后用终端号来踢

# 工作管理

* 把进程放入后台
  * 命令后面加&：后台继续运行
  * 在命令执行过程中，按下ctrl+z快捷键：放入后台即暂停
* 查看后台的工作：`jobs -l`：加l会显示工作号
* `bg %工作号`：恢复后台暂停的工作恢复到后台运行，但是不能恢复和前台有交互的命令比如`top命令`和`vim命令`
* `fg %工作号`：恢复后台暂停的工作恢复到前台运行
* **注意：工作号≠PID**

# 系统资源查看

* `vmstat 刷新延时(s) 刷新次数`：**监视系统资源**使用情况，和top内容差不多，但更简洁

* `dmesg`：**开机时内核检测**，一般结合grep使用

* free：查看**内存使用**情况

  -b：以字节为单位显示

  -k：以KB为单位显示（默认就是）

  -m：以MB为单位显示

  -g：以GB为单位显示

* 查看**CPU**信息：

  * `cat /proc/cupinfo`:每次开机都会更新
  * `dmesg | grep CPU`
  * `uptime`：实际就是top命令第一行

* `uname`：查看**系统与内核相关信息**

  -a：查看系统所有相关信息

  -r：查看内核版本

  -s：查看内核名称

* 判断当前系统的**位数**：file /bin/ls（通过系统外部命令的位数来推测）

* `lsb_release -a`：查询Linux系统的**发行版本**

* `lsof -p PID`：列出进程调用的文件

# 系统定时任务

**前提**：必须启动crond服务：`service crond restart`并且`chkconfig crond on`，Linux系统都是默认启动和自启动的

* crontab：设置系统定时任务

  -e：编辑crontab定时任务

  -l：查询crontab任务

  -r：删除当前用户所有的crontab任务

  打开文件编辑的格式是：`* * * * * command`

  * 第一个*：分钟（0-59）
  * 第二个*：小时（0-23）
  * 第三个*：天（1-31）
  * 第四个*：月（1-12）
  * 第五个*：星期（0-7，0和7都代表星期日）

  特殊符号：<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225045.png" style="zoom:50%;" />

  
