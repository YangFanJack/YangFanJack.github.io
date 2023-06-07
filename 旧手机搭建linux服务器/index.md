# 旧手机搭建linux服务器


### 硬件&软件准备：

**硬件：**

* 一台已root的安卓手机

**软件：**

* Linux Deploy：`https://github.com/meefik/linuxdeploy/releases`
* busyBox：`https://github.com/meefik/busybox/releases`

### 开干

1. 点开busyBox直接安装，记住安装地址
2. 点开linux deploy，点击左上角三条杠，修改系统名字，然后进入设置，自己根据需要开关选项
3. 在PATH环境变量里写上busyBox的安装路径
4. 点击右下角的属性按钮，根据需要安装linux发行版
5. 我选的是ubuntu，考虑到国内网络环境，将源地址改为`http://mirrors.ustc.edu.cn/ubuntu-ports/`
6. 在镜像大小里最好分多点空间，默认只有2G，如果有外置sd卡最好把镜像地址改为sd卡的地址，这样能自行分区更合理。
7. 修改初始用户的username和password，本地化选择语言，推荐英文
8. 初始化，挂载，SSH，声音服务 推荐都弄上
9. 返回主界面，点击右上角三个点开始安装，等待一会儿，直到出现deploy字样，说明安装成功
10. 此时先点击停止，在点击启动，就可以根据局域网ip通过ssh来远程连接安卓上linux容器了。

### 配置

做一些配置

```bash
apt update
apt upgrade
apt install gcc g++ vim git openjdk-8-jdk net-tools bash-completion htop

# LinuxDeploy下ping命令无法运行，提示socket：权限不足，原来android里将一些硬件使用（包括网络）的权限归到不同的用户组. 　而3003就是关于网络权限的组，称为inet. 上述命令是在debian的用户权限系统中给root 添加inte权限．
usermod -G 3003 root

# 修改root的密码
su - root
passwd
```

### 安装samba服务

```bash
#安装samba
apt install samba samba-common
#选定需要共享的文件夹
mkdir XXX
#设置文件夹权限
chmod 777 XXX
#设置samba用户密码
useradd smbUser
smbpasswd -a smbUser
smbpasswd -xx smbUser
#配置samba的配置文件
vim /etc/samba/smb.conf
[share]
comment = share folder
browseable = yes
path = XXX
create mask = 0700
directory mask = 0700
valid users = smbUser
force user = smbUser
force group = smbUser
public = yes
available = yes
writable = yes

#启动服务
service smbd restart

```

尝试了这种通过smb协议实现局域网共享文件的方式，能实现。但是不知道为什么不能共享挂载到安卓的目录，所以有了下面的方案。

### File Brower

1. 可以采用网盘实现方式，有很多相似的产品：OwnCloud，NextCloud，Seafile都可以，但是这些都有需要依赖的环境，部署相对麻烦，我选择了一个只需简单配置就能使用的私人网盘：File Brower

   File Browser 是一个基于 Web 的文件管理器。它可以使你随时随地的对设备的文件进行基本的管理操作，如：创建、删除、移动、复制等。它除了可以让你进行文件管理之外，还有一些其他的功能。它支持多个用户的管理，而且每个用户可以拥有自己可以访问的文件和权限。它还支持文件分享，就行网盘那样，你可以通过它来向你的朋友分享文件。你还可以用它来执行一些 Linux 命令，比如你想要在当前目录下克隆一个代码库，就可以用它来执行`git`等命令。

   ```bash
   #安装(之前可能需要配置hosts)
   curl -fsSL https://filebrowser.xyz/get.sh | bash
   #创建配置数据库
   filebrowser -d /etc/filebrowser.db config init
   #设置监听地址
   filebrowser -d /etc/filebrowser.db config set --address 0.0.0.0
   #设置监听端口
   filebrowser -d /etc/filebrowser.db config set --port 8888
   #设置语言环境
   filebrowser -d /etc/filebrowser.db config set --locale zh-cn
   #设置日志位置
   filebrowser -d /etc/filebrowser.db config set --log /var/log/filebrowser.log
   #添加一个用户（已有一个默认的admin:admin用户）
   filebrowser -d /etc/filebrowser.db users add username password --perm.admin
   #启动服务 & 设置别名简化启动
   filebrowser -d /etc/filebrowser.db
   vim ~/./bashrc
   alias runnas="filebrowser -d /etc/filebrowser.db"
   
   #支持Https(其中example.com.crt和example.com.key分别是 SSL 证书和**路径，根据自身情况进行更改。配置完 SSL 后，只可以使用 HTTPS 访问，不可以使用 HTTP)
   filebrowser -d /etc/filebrowser.db config set --cert example.com.crt --key example.com.key
   #取消 SSL
   filebrowser -d /etc/filebrowser.db config set --cert "" --key ""
   ```

2. 开机自启 & 后台运行问题
   两种方法：nohup和systemd

   * 第一种

     1. 运行：`nohup filebrowser -d /etc/filebrowser.db >/dev/null 2>&1 &`

     2. 停止运行：`kill -9 $(pidof filebrowser)`

     3. 开机启动：`sed -i '/exit 0/i\nohup filebrowser -d \/etc\/filebrowser.db >\/dev\/null 2>&1 &' /etc/rc.local`

     4. 取消开机启动：`sed -i '/nohup filebrowser -d \/etc\/filebrowser.db >\/dev\/null 2>&1 &/d' /etc/rc.local`

   * 第二种

     1. 操作百度
     2. 运行：systemctl start filebrowser.service
     3. 停止运行：systemctl stop filebrowser.service
     4. 开机启动：systemctl enable filebrowser.service
     5. 取消开机启动：systemctl disable filebrowser.service
     6. 查看运行状态：systemctl status filebrowser.service
