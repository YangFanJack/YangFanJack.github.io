# linux权限管理


# ACL权限

##     ACL权限的简介和开启方式

* 任何一个文件在一个时刻只能有一个所有者和所属组
* ACL权限用来解决文件的权限身份不够用的情况

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224537.png" style="zoom:80%;" />

* ACL权限需要分区支持：dumpe2fs -h /dev/sda5/查看是否支持acl选项，如果不支持。可以临时开启：mount -o remount,acl/ 或者永久开启：vim /etc/fstab然后重启系统
* ACL权限类似于windows的权限设置方法，就是不考虑user，group和others的权限设置，单独添加一个用户或者一个用户组对一个文件或者目录的权限

##     查看与设定ACL权限

* `getfacl 文件名`：查看ACL的权限

* `setfacl 选项 文件名`

  `setfacl -m u/g/m:用户名/组名/不写:权限 文件名`：设定文件的ACL权限

##     最大有效权限与删除ACL权限

* 最大有效权限mask：可以通过`getfacl 文件名`来查看ACL的权限，里面有mask，可以通过控制mask的值来修改默认最大有效权限。
* 需要注意的是mask权限不会影响当前文件所有者，只会影响ACL权限和所属组的权限
* `setfacl -x u/g:用户名/组名 文件名`：删除文件指定的ACL权限
* `setfacl -b 文件名`：删除文件所有ACL权限

##     默认ACL和递归ACL权限（只能针对目录）

* `setfacl -m d:u/g:用户名/组名:权限 文件名`：设定父目录的默认ACL权限，父目录里所有的新建文件都会继承父目录的ACL权限

* `setfacl -k`：删除父目录的默认ACL权限

* `setfacl -m -R ...`：递归设定文件夹的ACL权限

# 文件特殊权限

##     SetUID：在所有者的x位置上变成了s

* **四个条件缺一不可：**

  只有**二进制文件**（例如命令和脚本文件）才能设定SUID权限

  命令执行者必须对该程序**有x权限**

  命令执行者会在执行的时候获得该程序文件的**属主身份**

  SUID权限只在该程序执行**过程中生效**，也就是身份改变旨在程序执行过程中有效

* passwd命令拥有SetUID权限，所以普通用户能修改自己密码

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224547.png" style="zoom:80%;" />

* 我们通常会看到**4777，2777，1777**的权限标识，依次是加了**SUID，SGID，SBIT**权限

* 可以用`chmod`来赋予和删除SUID

* **SetUID是非常危险的，一个命令只要有了s权限，例如passwd命令，普通用户就可以通过执行这个命令获得passwd的属主身份，也就是进入root权限。**

##     SetGID：在所属组的x位置上变成了s

* **针对二进制文件，三个条件缺一不可：**

  命令执行者必须对该程序**有x权限**

  命令执行者会在执行的时候获得该程序文件的**所属组身份**

  SUID权限只在该程序执行**过程中生效**，也就是所属组身份改变旨在程序执行过程中有效

  注：例如/usr/bin/locate命令

* **针对目录，三个条件缺一不可：**

  普通用户必须对此目录**拥有r和x权限**，才能进入该目录

  普通用户在此目录中的**有效组会变成此目录的所著组**

  若普通用户对此目录拥有**w权限**时，**新建的文件的默认组不是文件自己的初始组，而是这个目录自己的所属组**

* 可以用`chmod`来赋予和删除SGID

##     Sticky BIT(黏着位)：在其他人的x位置上变成了t

* **四个条件缺一不可：**

  只有目录才能设定SBIT权限

  **普通用户**必须对该目录**有x和w权限**

  有了SBIT，**普通用户**即使有目录的w权限，也不能删除其他用户建立的文件

##     需要注意的安全性：

* **需要定期对系统中含有SUID或者SGID权限的文件进行检查，如果有异常多出来的含有该权限的文件，如果多出来了，是一个极大的安全隐患，需要手工清除。**

# 文件系统属性chattr权限

* **chattr：** change file attributes on a linux file system

* `chattr [+-=] [选项] 文件或目录名`：增加，减去，等于chattr权限

  i选项：

  ​	1.对文件：不允许任何用户（包括root用户）对文件进行任何修改，只能读

  ​	2.对目录：任何用户（包括root用户）只能在目录下修改文件内容，但是不能删除和创建文件

  a选项：

  ​	1.对文件：任何用户（包括root用户）只能对文件增加数据，但是不能删除和修改现有数据

  ​	2.对目录：任何用户（包括root用户）只能在目录中建立和修改文件里的内容，但是不能删除文件

* 查看文件系统属性：lsattr

  lsattr 选项 文件名

  ​	-a选项：显示所有文件和目录

  ​	-d选项：若文件时目录，仅列出目录自己的属性

# 系统命令sudo权限

* 之前学的是对文件的操作权限，sudo是对系统命令的权限。sudo权限是root把本来只能超级用户执行的命令赋予普通用户的执行

* root权限先执行命令visudo命令修改/etc/sudoers文件

  在这一行`root   ALL=(ALL)    ALL`下面添加例如：

  ​               ` jack   ALL=/sbin/shutdown -r now`：授权jack能重启服务器的权限

* 然后相应用户或者用户组用`sudo -l`查看自己能用那些sudo命令，然后`sudo 命令`执行
