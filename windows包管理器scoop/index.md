# Windows包管理器scoop


**对于熟悉linux的同学，apt和yum等等这些包管理器肯定很熟悉，Windows下也有一个类似的好用的包管理器scoop，用scoop安装一些环境配置软件就会省去手动配置环境变量的步骤，并且里面的软件还很齐全，wonderful!**

## 安装

* 管理员身份打开终端，设置用户变量
  ```powershell
  [environment]::setEnvironmentVariable('SCOOP','C:\software\scoop','User')
  ```
* 启用
  ```powershell
  $env:SCOOP='C:\software\scoop'
  ```
* install scoop
  ```powershell
  iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
  # 或者
  iwr -useb get.scoop.sh | iex
  ```
* 配置全局变量
  ```powershell
  [environment]::setEnvironmentVariable('SCOOP_GLOBAL','C:\software','Machine')
  ```
* 启用
  ```powershell
  $env:SCOOP_GLOBAL='C:\software'
  ```
* 设置代理or修改镜像
  ```bash
  scoop config proxy 127.0.0.1:8889
  scoop config SCOOP_REPO https://gitee.com/squallliu/scoop
  ```

## 目录介绍
* apps——所有通过scoop安装的软件
* buckets——管理软件的仓库，用于记录哪些软件可以安装、更新等信息，默认添加main仓库，主要包含无需GUI的软件，可手动添加其他仓库或自建仓库，具体在推荐软件仓库中介绍
* cache——软件下载后安装包暂存目录
* persit——用于储存一些用户数据，不会随软件更新而替换
* shims——用于软链接应用，使应用之间不会互相干扰，实际使用过程中无用户操作不必细究

## 使用
* 基础命令：
  ```bash
  scoop help：查看scoop的使用方式和命令
  scoop status：检查scoop是否最新
  scoop update：更新scoop库
  scoop update XXX：更新某个软件
  scoop update *：更新所有软件
  scoop search XXX：搜索软件
  scoop install XXX：安装软件
  scoop insrall -g XXX：全局安装软件
  scoop hold XXX：锁定软件版本
  scoop info XXX：查询软件简要信息
  scoop home XXX：进入软件官网
  scoop uninstall XXX：卸载软件
  scoop uninstall -g XXX：全局卸载软件 
  scoop list：列出安装的软件
  scoop bucket list：列出添加的仓库
  scoop bucket add XXX：添加仓库
  scoop bucket rm XXX：删除仓库
  ```
* App Manifest：
  * App Manifest 表示应用程序清单，它是一个 JSON 文件，定义了下载、校验、安装软件等信息，一个 App Manifest 就代表了一个软件，许多 App Mainifest 就组成了 Bucket，可以表示为一个软件仓库
  * 一般在scoop的根目录中的buckets目录中的每个仓库目录中的bucket目录下，有仓库下所有软件的App Manifest json文件
* 软件备份，恢复：
  * scoop提供了导出选项，将软件导出为装备文件：`scoop export > scoopfile.json`
  * 从json文件导入：`scoop import <path/url to scoopfile.json>`

## 添加仓库
```bash
scoop bucket add main
scoop bucket add extras
scoop bucket add games
scoop bucket add nerd-fonts
scoop bucket add nirsoft
scoop bucket add java
scoop bucket add php
scoop bucket add nonportable
scoop bucket add versions
scoop bucket add dorado https://github.com/h404bi/dorado
scoop bucket add echo https://github.com/echoiron/echo-scoop
scoop bucket add MorFans-apt https://github.com/Paxxs/Cluttered-bucket.git
scoop bucket add zapps https://github.com/kkzzhizhou/scoop-zapps
scoop bucket add raresoft https://github.com/L-Trump/scoop-raresoft
scoop bucket add lemon https://github.com/hoilc/scoop-lemon
```
## 常用软件
```bash
# Daily
scoop install 7zip
scoop install aria2
scoop install ccleaner
scoop install copyQ
scoop install draw.io
scoop install filezilla
scoop install firefox
scoop install googlechrome
scoop install foobar2000
scoop install geekunintaller
scoop install keepass
scoop install motrix
scoop install obx-studio
scoop install obs-plugin-droidcam
scoop install scoop-completion
scoop install sudo
scoop install sumatrapdf
scoop install utools
scoop install virtualbox-np
scoop install vlc
scoop install vmware-horizon-client-np
scoop install wget
scoop install peazip
# Development
scoop install joplin
scoop install picgo
scoop install idea@2021.2.4
scoop install clion
scoop install vscode
scoop install dbeaver
scoop install another-redis-desktop-manager
scoop install vim
# Environment
scoop install nodejs-lts@18.16.0
scoop install hugo-extended@0.109.0
scoop install openjdk8-redhat
scoop install maven@3.5.4
scoop install tomcat
scoop install git
scoop install gcc
```

### mysql57

```bash
#手动开启
mysql --standalone
mysql --console
#注册mysql为服务
mysqld --install MySQL --defaults-file="D:\software\apps\mysql57\current\my.ini"
#停止服务
sc stop MySQL
#删除服务
sc delete MySQL
```


