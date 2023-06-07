# linux的文件系统管理


# 分区和系统文件

* 对于硬盘分区形式是**MBR**的，主分区+扩展分区<=4个。如果是IDE硬盘，Linux最多支持59个逻辑分区；如果是SCSI硬盘，Linux最多支持11个逻辑分区
* 对于**GPT**分区形式而言，没有上述限制
* Windows文件系统为FAT16、FAT32、FAT64和NTFS。而Linux的文件系统可分为ext2、ext3、ext4、swap和vfat。
* 格式化的目的就是写入文件系统

# 文件系统常用命令

##     df命令，du命令，fsck命令，dumpe2fs命令

* `df -a -h`：显示所有分区&人性化显示。(一般用来统计系统空间大小)
* `du -a -h -s`：弥补ls命令的不足，可以统计文件夹包括里面的内容的大小而不是单单文件夹的大小。(一般用来统计文件大小)
* **df和du的区别：** du只是面向文件的，只会计算文件或目录占用的空间；df是从文件系统角度考虑的，不光要考虑文件占用的空间，还要统计被命令或程序占用的空间（最常见的就是文件已经删除但是程序并没有释放空间）
* `fsck -a -y 分区设备文件名`：文件系统修复命令，不需要自己手动执行
* `dumpe2fs 分区设备文件名`：显示ext2、ext3、ext4文件系统的超级块和块组信息

## 挂载命令

* 将设备文件名和绑定到盘符(挂载点)上，Windows是自动，Linux默认是手动分配
* `mount -l`：查询异已挂载的设备，加-l会显示卷标名
* `mount -a`：依据配置文件`/etc/fstab`中的内容，自动挂载
* `mount [-t 文件系统]  [-L 卷标名] [-o 特殊选项] 设备文件名 挂载点` 

## 挂载光盘和U盘

* Linux中的分区时开机自动挂载，但是光盘和U盘时手动挂载，系统准备了/mnt/和/media/来挂载

* **挂载光盘步骤：**（sr0和cdrom都可以作为设备文件名）

  `mkdir /mnt/cdrom/`：建立挂载点

  `mount -t iso9660 /dev/cdrom /mnt/cdrom/`或者`mount /dev/sr0 /mnt/cdrom`：挂载

  `cd /mnt/cdrom/`：去挂载点访问

  `umount 设备文件名或挂载点`：在拿走光盘之前需要先卸载光盘

* **挂载U盘步骤：**（U盘的设备文件名是自动识别的，用`fdisk -l`查看）

  `mkdir /mnt/usb/`：建立挂载点

  `mount -t vfat /dev/sdb1 /mnt/usb/`：挂载

  `cd /mnt/usb/`：去挂载点访问

  `umount 设备文件名或挂载点`：在拔U盘之前需要先卸载光盘

  **注意：挂载U盘只能用本机或者虚拟机，不能用远程工具**

  ​			**Linux默认是不支持NTFS文件系统的外设的，U盘只能FAT格式的**

## Linux如何支持NTFS文件系统

* Linux系统的内核中已经把市面上常见的驱动包括了，所以安装系统后一般不需要安装驱动，Linux会自动匹配识别，如果Linux默认没有把一个文件系统的驱动加载进来，我们就需要手动去安装驱动。

* 几种让Linux支持NTFS文件系统的方法：

  * 内核重新编译
  * 第三方的插件：[ntfd-3g](http://www.tuxera.com/community/ntfs-3g-download/)

* 内核编译太麻烦，太高端，一般选择“ntfd-3g插件”来实现

* 安装步骤：

  `tar -zxvf ntfs-3g_ntfsprogs-2013.1.13.tgz`：解压

  `cd ntfs-3g_ntfsprogs-2013.1.13`：进入解压目录`

  `./configure：`编译前准备

  `make`：编译

  `make install`：编译安装

  `mount -t ntfs-3g 设备文件名 挂载点`：挂载NTFS分区的硬盘

# fdisk分区

##     fdisk命令分区过程

* 虚拟机添加新硬盘，并用`fdisk -l`查询新硬盘是否被识别

* 若已识别新硬盘/dev/sdb/，就开始为新硬盘分区，`fdisk /dev/sdb/`进入fdisk交互模式

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224418.png" style="zoom: 80%;" />

* partprobe：分完区后必须执行这个命令，重新读取分区表信息

* 然后格式化分区：`mkfs -t ext4 /dev/sdb1`   `mkfs -t ext4 /dev/sdb5 `

* 最后创建挂载点并挂载：`mkdir /disk1`     `mount /dev/sdb1 /disk1/`   `mkdir /disk5     ` `mount /dev/sdb1 /disk5/`   

* fdisk -l：查看，两个分区挂载完毕

##     分区自动挂载和/etc/fstab文件修复

* 上面的mount命令挂载只能临时生效，每次重启都要手动挂载硬盘，要想永久生效，就要修改/etc/fstab配置文件的内容。

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224424.png" style="zoom:80%;" />

* 添加了挂载信息后，执行mount -a，依据配置文件`/etc/fstab`中的内容，自动挂载

* 万一`/etc/fstab`文件写错了，会导致系统崩溃

* **/etc/fstab文件修复**：出错后按提示进入root操作界面，发现`/etc/fstab`文件是只读，执行`mount -o remount,rw /`，就可以修改`/etc/fstab`文件了，就可以修复了。




