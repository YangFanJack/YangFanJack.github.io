
# 日志管理简介

* **百度百科简介**：系统日志是记录系统中硬件、软件和系统问题的信息，同时还可以监视系统中发生的事件。用户可以通过它来检查错误发生的原因，或者寻找受到攻击时攻击者留下的痕迹。系统日志包括系统日志、应用程序日志和安全日志。

* **服务器出现问题先查看日志**，才能找准原因

* CentOS7中原来的日志服务syslogd被rsyslogd取代，两者兼容

* 确认服务是否启动和自启动：

  * `ps aux | grep rsyslogd`
  * `chkconfig --list | grep rsylog`

* **常见的日志的作用：**

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224558.png" style="zoom: 67%;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224604.png" style="zoom: 67%;" />

* RPM包安装的服务日志也会在/var/log/目录下

* 源码包安装的服务日志在源码包指定目录（一般是/usr/local）中，这些日志不是有rsyslogd服务来管理的，而是由各个服务使用自己的日志管理文档来记录自身日志

# rsyslogd日志服务

##     日志文件格式

* 事件产生的时间
* 产生事件的服务器的主机名
* 产生事件的服务名或程序名
* 事件的具体信息

##     /etc/rsyslog.conf配置文件

​		**格式：**

* authpriv.*                                        /var/log/secure

  服务名称[连接符号]日志等级         日志记录位置

* 服务名称，连接符，日志等级，日志记录位置都有多个，内容很多，自行百度。

# 日志轮替

**如果日志都记录在一个文件中，那么可能会占据大量存储空间，纯文本文档打开会非常慢，所以日志需要处理：切割（把大日志按天切割成小的）+轮换（删除旧的，保存新的）**

* 如果配置文件中由“dateext”参数，那么日志会用日期作为后缀，只需要保存指定的日志个数，删除多余的日志文件即可

* 如果没有“dateext”参数，那么日志文件就需要改名了，当第一次使用日志轮替时，当前的“secure”日志会自动改名为“secure.1”，然后新建“secure”日志。第二次时，1变2，0变1，又新建0，以此类推

* 配置文件：`/etc/logrotate.conf`

  包含参数：daily，weekly，monthly，rotate，compress，create mode owner group等

  分别表示：轮替周期时每天，每周，每月，保存日志文件的个数，旧日志是否压缩，建立新日志同时指定权限

* 只要是RPM包安装的服务，它默认已经支持轮替，但是源码包安装的服务需要`vim /etc/logrotate.conf`，然后手动加入轮替

  FOR EXAMPLE:

  ```c++
  /usr/local/apache2/logs/access_log{
      daily    //每天备份
      create   //备份的时候创建新的日志
      rotate 30   //保留30天
  }
  ```

* `logrotate -v /etc/logrotate.conf`：显示日志轮替过程

* `logrotate -f /etc/logrotate.conf`：强制轮替，不管日志轮替的条件是否已经满足