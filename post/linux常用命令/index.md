
* **Linux作为一个网络操作系统，命令行才是王道，而不是图形。**
* **Linux系统管理的命令是它正常运行的核心，与之前的DOS命令类似。**
* **linux命令在系统中有两种类型：内置Shell命令和Linux命令**

# linux各目录的作用

* `/bin/`：存放系统命令

* `/sbin/`：存放系统目录，只有超级用户能用

* `/usr/bin/`：存放系统命令，单用户模式不能执行

* `/usr/sbin/` ：存放系统命令，只有超级用户能用，单用户模式不能执行

* `/boot/` ：系统启动目录，内核和启动引导程序

* `/dev/` ：硬件设备文件目录

* `/etc/` ：linux默认的配置文件保存目录

* `/home/`：普通用户家目录

* `/root/`：超级用户家目录

* `/lib/`：系统调用的函数库

* `/lost+found/`：当系统意外崩溃时，每个分区都含有的存放的文件碎片用来修复

* `/media/`：挂载目录，挂载媒体设备

* `/mnt/`：挂载目录，挂载U盘，移动硬盘，和其他操作系统的分区

* `/misc/`：挂载目录，挂载NFS服务的共享目录

* `/opt/`：第三方安装的软件的保存目录，也可以放到/usr/local/下

* `/proc/`：存放在内存里面，存放系统的内核，进程，外部设备

* `/sys/`：存放在内存里面，存放系统的内核相关的东西

* `/srv/`：服务数据目录

* `/tmp/` ：临时目录，可以清空

* `/usr/`：系统资源目录

* `/var/`：动态资源保存目录，日志，邮件，数据库

# 服务器注意事项

1. 远程服务器不允许关机，只能重启
2. 重启时应该关闭服务
3. 不要在服务器访问高峰运行高负载命令
4. 远程配置防火墙时不要把自己踢出服务器
5. 指定合理的密码规范和定期更新
6. 合理分配权限
7. 定期备份重要数据和日志

# Linux常用命令

## 	文件处理命令

### 		ls

* `ls -l`：查看文件所有属性
* `ls -d`：查看目录本身
* `ls -i`：查看文件的inode号
* `ls -h`：人性化显示
* `ls -s`：显示文件占用的块的数量
* `ls -a`：查看隐藏文件

### mkdir

* `mkdir -p`：递归创建目录

### cd&pwd

### rmdir

### cp

* `cp -r`：复制目录

* `cp -p`：原文件和新文件属性完全一致

  注：复制的同时可以改名

### mv

​		注：移动的同时可以改名

### 		rm
* `rm -r`：文件夹递归删除
* `rm -f`：强制删除，不询问

### touch

### cat&tac

* `cat -n`：显示行号，tac没有-n选项


### more

​		空格翻页，回车换行，q退出

### 		less

​		空格翻页，回车换行，q退出，还可以pageup回翻页，上箭头网上翻一行，还可以搜索：/关		键词，再按n搜索下一个匹配的

### 		head
* `head -n`：指定看前几行，默认前10行


### tail

* `tail -n`：指定看后几行，默认后10行

* `tail -f`：动态看文件动态变化


### ln

* `ln -s`：生成软链接

* `ln`：生成硬链接，和cp -p的区别是硬链接文件和原文件可以同步更新，硬链接通过inode号来区分。和软连接的区别：1.硬链接不能跨分区   2.硬链接不能针对目录

## 权限管理命令

### 		chmod

* `chmod [u/g/o/a] [+/-/=] [r/w/x]`

* `chmod 三位数XXX`：r—4，w—2，x—1：举例：rwxr-xr-x：755

* `chmod -R`：递归修改目录及目录下的权限

  注释：深入理解文件的wrx和目录的wrx的意义

###         chown

* `chown [用户名]`：只有管理员root能执行

###         chgrp

* `chgrp [组名]`：只有管理员root能执行

  **注：每个文件都只有一个user和一个group，某些others拥有拥有共同的权限，这些others在一个组里，也就是group所属组。所属者user可以不在所属组group里面，并且一个用户可以在不同的组里。** 相关的命令是：

  ~~~powershell
  groupadd YYY          //创建组
  useradd -g YYY XXX    //创建用户并将YYY设置为其主要组
  usermod -g ZZZ XXX    //修改用户XXX的主要组为ZZZ
  ~~~

###         umask

* `umask -S`：人性化显示默认权限

* `umask`：显示掩码（非人性化）

  注：Linux中，默认创建的文件是没有可执行权限的，所以文件是666，目录是777

* `umask 三位数`：修改掩码，使默认权限变化

##  文件搜索命令

### 		find：精准搜索

* `find -name`：根据文件名搜索

* `find -iname`：根据文件名搜索，不区分大小写

  注：通配符：*，？，[]，

* `find -size [+/-]`：+：大于，-：小于，只有数据块（0.5KB）个数：等于

* `find -user`：根据所有者查找
* `find -group`：根据所属组查找
* `find -amin [-/+]分钟数`：查找指定时间内或者超过该时间被修改过**访问时间**的文件
* `find -cmin [-/+]分钟数`：查找指定时间内或者超过该时间被修改过**文件属性**的文件
* `find -mmin [-/+]分钟数`：查找指定时间内或者超过该时间被修改过**文件内容**的文件

* 还可以在两个查找条件之间加上`-a` 或者`-o` ，一个是同时满足，一个是满足任意一个即可。或者`-exec/-ok 命令 {}\; ` 对搜索结果执行操作

* `find -type`：根据文件类型查找
* `find -inum`：根据inode号查找

###         locate：模糊查找

* 系统里所有的文件都会定期收录到/var/lib/mlocate.db这个文件库里，locate就是在这个里去找，但是新的文件没别收录进去，所以就找不到，需要手动更新文件资料库`updatedb` （但是tmp目录下不收录进去）
* locate -i：不区分大小写

###         which

* which 命令名：查找命令的地址和别名alias

###         whereis

* whereis 命令名：查找命令地址和其帮助文档的位置

###         grep：在文件里面查找

* grep -i：不区分大小写，查找指定字串所在的行
* grep -v：排除指定字串所在的行

## 帮助命令

###         man和info

* man 命令名/配置文件名：查看命令和配置文件的帮助信息，浏览和more操作一样
* 帮助类型里1是命令的帮助，5是配置文件的帮助
* For Example：man 1 passwd，man 5 passwd

###         whatis

* 更加简单的命令查询

###         apropos

* 更加简单的配置文件查询

###         --help

* 命令 --help：得到信息更加简单

###         help

* shell的**内置命令**的帮助

## 用户管理命令

###         useradd和passwd

* 新建用户和修改密码

###         who和w

* 查看当前登录用户名：tty是本地登录，pts表示远程登录

## 压缩解压命令

​	几种压缩格式

> .gz
>
> .zip
>
> .bz2

###         gzip和gunzip/gzip -d

* 只能压缩文件，不能压缩目录，并且不保留原文件

###         tar

* tar -c：打包

* tar -x：解包

* tar -v：显示详细信息

* tar -f：指定文件名

* tar -z：打包的同时压缩，或者解包的时候解压缩,适用于压缩解压gz

* tar -j：同-z，适用于压缩解压bz2

  For Example：`tar -cvf XXX.tar YYY`   `gzip XXX.tar` ，最后生成`XXX.tar.gz` 。或者直接`tar -zcvf`打包压缩一部合成，反向是`tar -zxvf` 

###         zip和unzip：压缩比不高

* 压缩后能保留原文件

* zip -r：压缩目录

###         bzip2和bunzip2：gzip的升级版本，压缩比较好

* bzip2 -k：压缩的同时保留原文件
* bunzip -k：解压的同时保留原文件

##     网络命令

###         write

* write 用户名：给**在线用户**发信息，以ctrl+D保存结束

###         wall

* wall 信息内容：给**当前在线的所有用户**发送信息

###         ping

* 网络踩点，Linux会一直ping下去
* ping -c 次数：定义ping的次数

###         ifconfig

* 查看当前系统网卡信息和设置网卡信息（临时的）

###         mail

* 查看和发送邮件：不一定要在线
* mail 用户名：发动
* mail：查看的子命令：help(帮助)，数字(查看指定邮件)，h(列表)，d 数字(删)

###         last

* 日志查询命令，统计系统的所有登录信息

###         lastlog

* 查看用户最后登录的信息
* lastlog -u uid：查看指定用户上次登录的信息

###         traceroute

* traceroute ip地址：跟踪节点的路径

###         netstat：显示网络相关信息

* netstat -t：TCP协议

* netstat -u：UDP协议

* netstat -l：监听

* netstat -r：路由

* netstat -n：显示ip地址和端口号

  最常用的三种组合命令：

* netstat -tlun：查看本机监听的端口

* netstat -an：查看本机所有的网络连接

* netstat -rn：查看本机路由表

###         setup：redhat系linux独有

* 配置网络的工具：永久生效
* 配置完需要重启网络服务：`service network restart` 

##     挂载命令

###         mount

* mount [-t 文件系统] 设别文件名 挂载点
* For Example：`mount -t iso9660 /dev/sr0 /mnt/cdrom` 

###         umount

* umount 设备文件名：卸载

##   关机重启命令

###         shutdown

* shutdown -h 时间（now）：关机
* shutdown -r 时间：重启
* shutdown -c：取消前一个关机命令

###         其他关机命令：halt,poweroff,init 0

###         其他重启命令：reboot,init 6

###         init 

* 系统**运行级别**：0：关机，1：单用户，2：不含NFS服务的多用户，3：完全多用户，4：未分配，5：图形界面，6：重启
* /etc/inittab配置文件里面有运行级别的信息，方便查询，也可以改运行级别
* init 数字：设置系统运行级别，临时更改
* runlevel：查看当前运行级别

###         runlevel:查询系统运行级别

###         logout和exit

* 都是退出登录：logout退出是把因为你注销了登陆机而把你踢出的退出，exit是你直接退出连接的机器。
