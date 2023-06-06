
# CentOS 6的启动管理

* 运行级别：7个级别

  `runlevel`：查看运行级别

  `init 运行级别`：改变当前运行级别

  `vim /etc/inittab`：永久修改系统默认运行级别，写上`id:3:initdefault`

  不要把0和6设为默认级别

* **CentOS6系统启动过程：**（针对MBR模式）

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224437.png" style="zoom: 80%;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224453.png" style="zoom:67%;" />

**[配合视频食用](https://www.bilibili.com/video/av18156598/?p=103)**

# 启动引导程序grub

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224525.png" style="zoom: 50%;" />

* Grub配置文件：`/boot/grub/grub.conf`

  **格式：**

  default=0                          默认启动第一个系统

  timeout=5                         等待时间，默认是5秒

  splashimage=(hd0,0)/grub/splash.xpm.gz                    指定grub启动时的背景图像文件的保存位置

  hiddenmenu                     隐藏菜单

  title CentOS(2.6.32-279.el6.i686)                        标题

  root (hd0,0)                                                             指启动程序的保存分区

  kernel /vmlinuz-2.6.32-279.el6.i686 ro               定义了内核加载时的选项

  initrd /initramfs-2.6.32-279.el6.i686.img           initramfs内存文件系统镜像文件的所在位置