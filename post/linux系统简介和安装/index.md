
## 	UNIX和Linux发展史

​		1965年，贝尔实验室：MULTICS计划

​		69年，贝尔实验室的肯汤普森：为了游戏开发UNICS/UNIX系统

​		62年，美国军方：ARPA：阿帕网，NCP协议——》TCP/IP协议

​		71年，可汤普森和丹尼斯里奇发明C语言，重写UNIX

​		UNIX主要发行版本：AIX(IBM)、HP-UX(HP)、Solaris(Sun)、Linux(Intel,AMD......)、BSD

​		1991年，芬兰大学生Linus Torvalds开发linux内核。大学教授开发minix，但是不接受外来代码，所以李纳斯独自开发，由社区共同维护。

​		Linux内核版本号：主版本.次版本.末版本     [linux内核官网](www.kernel.org)

​		Linux发行版本：两大派系redhat和debian

## 	开源软件简介

​		商业软件和开源软件（开源≠免费）

​		开源软件：apache、NGINX、MySQL、php、mongoDB、python、Ruby、Perl、Go、Rust、Swift、Java

​		开源软件的特点：使用自由（绝大多数免费）、研究自由（源代码）、散步和改良的自由

​		支撑互联网的开源技术（LAMP）：Linux，Apache，MySQL，PHP

## 	Linux应用领域

1. 基于Linux的企业服务器

   [踩点网站](www.netcraft.com)：发数据包根据相应嗅探服务器

   [世界前500服务器](www.top500.org)

2. 嵌入式应用

   手机，平板：Android底层是Linux

   智能家电，航空系统，银行系统......

3. 在电影娱乐业

   特效，图形处理渲染

## 	Linux学习方法

​		Linux只考虑应用性和稳定性

​		善于观察提示信息，查找文档，自己解决问题

​		学习英文：Command not found和No Such file or directory

​		忘掉Windows的操作习惯

​		计划，专注，坚持，练习

# Linux系统安装

## 	VMware虚拟机安装

​		[VMware官网](www.vmware.com)下载，不推荐安装双系统

​		特点：

   1. 不需要分区就能在物理机上使用两种以上的操作系统

   2. 物理机和虚拟机能实现网络通信

   3. 可以设定并随时修改虚拟机操作系统的硬件环境

      要求：

1. CPU：主频1GHz以上

2. 内存：1GB以上

3. 硬盘：8GB以上

   镜像下载：

   [官网下载](https://www.centos.org/download/mirrors/):几个版本：DVD版本，Everything版本，minimal版本，LiveGnome版本，KdeLive版本，livecd版本，NetInstall版本

## 	系统分区

​		**主分区**：最多只能有4个

​		**扩展分区**：最多只能有一个；主分区加扩展分区最多有4个；不能写入数据，只能包含逻辑分区

​		**逻辑分区**：可以和主分区一样正确的写入数据和格式化

​		**注意：**这套视频录制时间较为久远，当时的硬盘分区形式是MBR的，所以上述的分区限制也只					是针对MBR分区形式，对于GPT分区形式而言，则没有上述限制了。

​		电脑根据主板的不同（BOIS或者UEFI），会决定硬盘选择MBR分区方案还是GPT分区方案：

1. **BIOS + MBR**

2. **UEFI + GPT**

   两者区别：

   1. 也就是说，电脑使用传统BIOS主板，建议使用MBR分区方案；电脑使用UEFI主板，建议使用GPT分区方案

   2. MBR分区表最多只能识别2TB左右的空间，大于2TB的容量将无法识别从而导致硬盘空间浪费；GPT分区表则能够识别2TB以上的硬盘空间。

   3. MBR分区表最多只能支持4个主分区或三个主分区+1个扩展分区(逻辑分区不限制)；GPT分区表在Windows系统下可以支持128个主分区。

   4. 在MBR中，分区表的大小是固定的；在GPT分区表头中可自定义分区数量的最大值，也就是说GPT分区表的大小不是固定的。

   **硬盘分区的作用：** 把一块大硬盘分成几块

   **格式化的作用：** 写入文件系统（1.把硬盘分成一个个等大小的数据块   同时2.建立一个inode列表）

   Linux中的**所有硬件都是文件**：

   硬盘文件名：

   1. IDE硬盘：/dev/hd[a-d]
   2. SCSI/SATA/USB硬盘：/dev/sd[a-p]
   3. 光驱：/dev/cdrom或/dev/sr0
   4. 鼠标：/dev/mouse

   分区文件名：

   1. /dev/hda[数字]:
   2. /dev/sda[数字]

   **挂载：** 给分区分配挂载点

   1. /根分区
   2. swap交换分区（内存两倍，最大不超多2GB）
   3. /boot启动分区（200MB足够）

   总结：

   1. 分区：把大硬盘分为小的分区
   2. 格式化：写入文件系统，同时会清空数据
   3. 分区设备文件名：给每个分区定义设备文件名
   4. 挂在：给每个分区分配挂载点，这个挂在点必须是空目录

## 	Linux系统安装

​		把镜像加进去，点击启动，然后用图形界面配置分区和其他的自定义选项，确定定义root用户的密码和普通用户的账号和密码。然后等待安装完成即可。

##  	远程登陆管理工具

​		三种网络连接方式：

1. 桥接模式：虚拟机使用物理网卡

2. NAT模式：虚拟机使用vmnet8虚拟网卡

3. Host-only模式：虚拟机使用vmnet1虚拟网卡，并且只能和本机通信

   临时配置ip：``ifconfig ens33 192.168.XXX.XXX`` 

   永久配置ip：``vim /etc/sysconfig/network-scripts/ifcfg-ens33`` 修改，[见教程](https://blog.csdn.net/weixin_44687427/article/details/88225478)

