
## Mint LInux安装概述

1. rufus录入iso镜像到U盘
2. 进入bios界面调节启动位置为U盘
3. 开机进入安装界面，下一步...
4. efi分区自动放入win10的efi分区，/分区45G，/home分区300G，swap分区1G

## 安装后配置

### 双系统时间不同步

```bash
sudo apt update
# 禁用Ubuntu的UTC
timedatectl set-local-rtc 1 --adjust-system-clock
# 更新本地时间
sudo apt install ntpdate
sudo ntpdate time.windows.com
# 将本地时间更新到硬件上
sudo hwclock --localtime --systohc
```

### 输入法里安装ficix & 安装搜狗输入法

* IDEA等jetbrain公司的IDE的搜狗输入法不跟随问题可以采用更改这些IDE的运行环境解决

### 完善mint的中文支持

```bash
sudo apt-get install language-pack-zh-hans language-pack-gnome-zh-hans

#添加微软雅黑字体
#雅黑字体文件放在 Windows/fonts/ 文件夹，或者在控制面板-字体中查找，两个文件名分别为：msyh.ttf和msyhbd.ttf。字体

# 在Linux mint 系统字体文件夹中创建对应的字体文件夹：
sudo  mkdir  /usr/share/fonts/msyh
# 移动字体文件到对应的文件夹下：
udo  mv  msyh.ttf msyhbd.ttf  /usr/share/fonts/msyh
# 更新字体：
sudo  fc-cache  -fv
```

### 安装typora

```bash
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add - # 添加公钥
sudo add-apt-repository 'deb https://typora.io/linux ./' # 添加typora仓库
sudo apt-get update 
sudo apt-get install typora # 安装typora
```

### 安装Qv2ray

[Qv2ray下载]([pan.baidu.com/s/1hPKp0V](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/1hPKp0V4J54YihTsh05zhOQ))：密码1lph

[插件下载]([https://pan.baidu.com/s/14quUKv](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/14quUKv1-9_QHYPXjHe-2Aw))：密码ha9k

* 制作启动项：

  ```bash
  [Desktop Entry]
  Name=qv2ray
  Comment=network ladder
  Exec=/home/jack1024/Programs/Qv2ray/Qv2ray.v2.6.3.AppImage
  Icon=/home/jack1024/Programs/Qv2ray/qv2ray.png
  Terminal=false
  Type=Application
  Categories=Network;
  ```

### 主题美化

* [Cinnamon主题](https://www.pling.com/s/Cinnamon/)：选择Full Icon Themes 和 Cinnamon Themes分别下载图标包**Papirus**和主题包**Adapta Colorpack Normal**
* 在Gnome主题的GRUB Thmes中选一个开机引导的主题下载安装
* 面板移到顶部，调整面板控件摆放
* 安装**扩展、小程序、桌面小工具**等插件
* 选一张好看的壁纸

![Linux Mint美化后桌面](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imggdfs.jpg)


### 安装各种软件

#### vim简单配置

```bash
cd ~
vim .vimrc

set nu
set tabstop=4
set nobackup
set cursorline
set ruler
set autoindent
set mouse=c
set showmode    
set hlsearch
```

#### 安装&配置git

```bash
git config --global user.name "yangfanjack"
git config --global user.email "1144536063@qq.com"
ssh-keygen -t rsa      # 生成两把钥匙
ssh -T git@github.com  # 验证ssh配置是否配置好
```

#### 安装postman

```bash
[Desktop Entry]
Name=postman
Comment=API development
Exec=/home/jack1024/Programs/Postman/Postman
Icon=/home/jack1024/Programs/Postman/app/resources/app/assets/icon.png
Terminal=false
Type=Application
Categories=Network,Development;
```

#### 安装wireshark

```bash
# 使用usermod命令将您自己添加到wirehark组
sudo usermod -aG wireshark $(whoami)
```

#### 安装picgo-core并配置

```bash
# 安装nvm和nodejs
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash

nvm --version
nvm ls-remote
nvm install v12.18.0
nvm ls

# 安装gitee插件
cd /home/jack1024/.config/Typora/picgo/linux
./picgo install gitee-uploader

# 配置picgo-core
{
  "picBed": {
    "current": "gitee",
    "uploader": "gitee",
    "gitee": {
      "repo": "gitee用户名/仓库名",
      "branch": "master",
      "token": "你的私人令牌",
      "path": "img/  #你的仓库下的路径",
      "customPath": "default",
      "customUrl": ""
    },
    "transformer": "path"
  },
  "picgoPlugins": {
    "picgo-plugin-gitee-uploader": true
  },
}
```

#### 安装zsh & oh-my-zsh

```bash
# 安装zsh
sudo apt-get install zsh
# 安装oh-my-zsh
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
# 移动配置文件到home
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
# 设置zsh为默认shell
chsh -s /bin/zsh
# 优化zsh
# 添加nvm环境变量到.zshrc
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

# zsh速度优化
# Add default node to path
export PATH=$HOME/.nvm/versions/node/v10.22.0/bin:$PATH
# load nvm
export NVM_DIR="$HOME/.nvm"
[[ -s "$NVM_DIR/nvm.sh" ]] && source "$NVM_DIR/nvm.sh" --no-use

# 插件安装
apt install bat
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open

plugins=(其他的插件 zsh-autosuggestions zsh-syntax-highlighting git-open Z)
HIST_STAMPS="yyyy-mm-dd"

source ~/.zshrc

# 对于powerline主题的字体要求,进入fonts后bash .install.sh
https://github.com/powerline/fonts
```

#### 安装Powerlevel10k  zsh主题

```bash
# 下载Powerlevel10k
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k

# 配置.zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"

# 安装MesloLGS NF字体文件
https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k

# 自定义主题
p10k configure
```



#### 安装wps缺失字体

[字体下载地址](https://pan.baidu.com/s/1fn2sTNaqNmbK8BcS9iMSGw)：密码n9xc

```bash
sudo unzip wps_symbol_fonts.zip -d /usr/share/fonts/wps-office
```

进入wps-office目录，一个一个地安装

### 配置各种环境

```bash
sudo apt install openjdk-8-jdk gcc g++ cmake
```

#### 安装mysql

[官网下载](https://downloads.mysql.com/archives/community/)：安装5.7.31

解压，得到13个包，删除两个test包

```bash
sudo dpkg -i mysql-*.deb
sudo apt install -f
sudo dpkg -i mysql-*.deb
mysql -uroot -proot
show databases;
```

因为基于ubuntu20.04的mint20.1的默认mysql版本是mysql8，所以需要锁定版本

```bash
# 锁定软件包版本
sudo apt-mark hold mysql-common
sudo apt-mark hold mysql-client
sudo apt-mark hold mysql-server
# 检查标记为锁定的包
sudo apt-mark showhold
# 接触锁定
sudo apt-mark xxx
```



#### 安装tomcat

[官网下载](https://tomcat.apache.org/download-90.cgi)：安装9.0.43

解压后移动：

```bash
sudo mv apache-tomcat-9.0.43 /opt/
```

#### 安装maven

[官网下载](http://maven.apache.org/download.cgi)：安装3.6.3

解压后移动

```bash
sudo mv apache-maven-3.6.3 /opt/
```

配置环境变量

```bash
cd /etc/profile.d
sudo vim maven.sh

export M2_HOME=/opt/apache-maven-3.6.3
export CLASSPATH=$CLASSPATH:$M2_HOME/lib
export PATH=$PATH:$M2_HOME/bin

source maven.sh
```

拷贝setting.xml到~/.m2下

```xml
<!-- 修改respository-->
<localRepository>/home/jack1024/.m2/repository</localRepository>
<!-- 修改源-->
<mirror>
	<id>alimaven</id>
	<name>aliyun maven</name>
	<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
	<mirrorOf>central</mirrorOf>
</mirror>
```

IDEA和Clion配置

* 大小写不敏感：Editor，General，Code Completion，取消Match case
* 取消打开默认项目：Appearanc&Behavior，System Settings，取消Reopen
* 主题：Appearanc&Behavior，Appearance，Theme
* 编辑区主题：Editor，Color Scheme，Color Scheme Font
* 字体：Editor，Font
* 自动导包：Editor，General，Auto Import
* 自动编译：Build，Compiler，Build Project Auto，Compile independent

## 软件：

### 日常：

搜狗输入法：中文输入法

edge：微软浏览器

chrome：谷歌浏览器

qv2ray：科学冲浪

xdm：下载

utools：让你效率倍增的快速启动软件

每日英语听力：学英语

网易云音乐：在线听歌

1music：一个聚合听歌开源软件

audacious：本地听歌

gimp：图片处理

shotcut：视频剪辑

steam：游戏

vlc：视频播放器

telegram：加密聊天

qq for linux：复古风qq

wps：办公套件

百度网盘：限速达人

mindmaster：思维导图

### 开发

clion：C/C++开发IDE

idea：Java开发IDE

dbeaver：数据库管理客户端

filezilla：ftp客户端

smartgit：git客户端

typora：markdown笔记

virtualbox：虚拟机

vscode：强大拓展性文本编辑器

wireshark：网络抓包

zeal：离线文档

ARDM：redis管理客户端

postman：http调试

### 小工具

keepassXC：密码管理

copyQ：剪切板历史

caffeine：屏幕常亮

keepassxc：密码管理

新立得：apt包管理客户端

Gparted：磁盘管理

bleachbit：清理垃圾

zsh：舒服的shell

oh-my-zsh：zsh快速配置

tldr：查看命令帮助文档

htop：系统监视

kdeconnect：电脑手机强大交互软件

plank：dock栏

obs-studio：录屏

etcher：U盘镜像制作

Qredshift：根据时区自动调整屏幕亮度，温感



## 环境

jdk1.8

gcc g++

maven3.6.3

mysql5.7

cmake

tomcat9

nvm

node

## 插件

### 扩展

watermark

透明面板

### 小程序

Qredshift

系统监视器


## Linux系统卸载

在win10+linux双系统的环境下，如果要卸载linux系统，采用以下方式：

1. 进入win10系统，磁盘管理中直接清空linux的/分区和/home对应的磁盘分区
2. 如果安装linux系统时新建了linux的efi分区，在win10的磁盘管理工具中是删除不了的，推荐使用Genuis这款windows下的第三方磁盘管理软件来清除efi分区。
3. 如果安装linux系统时将linux的efi分区和win10的efi放在了一起，那么需要进行如下操作：

```bash
# 以管理员身份进入PowerShell
diskpart #进入diskpart
list disk #列出所有磁盘
select disk 0 #根据自己的情况选择efi分区对应的磁盘
list partition #列出该磁盘所有分区
select partition 1 #根据自己的情况选择efi分区对应的分区
assign letter=p: #为所选分区分配盘符p
exit #退出diskpart

p: #进入p盘
cd efi #进入efi文件夹，然后根据自己的情况删除相应的文件夹
```

4. 但是这个时候bios中还是有ubuntu的选项，可以通过easyUEFI或者Bootice来删除