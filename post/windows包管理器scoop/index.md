
**对于熟悉linux的同学，apt和yum等等这些包管理器肯定很熟悉，Windows下也有一个类似的好用的包管理器scoop，用scoop安装一些环境配置软件就会省去手动配置环境变量的步骤，并且里面的软件还很齐全，wonderful!**

## 安装

* **管理员身份打开终端，设置用户变量**

  ```powershell
  [environment]::setEnvironmentVariable('SCOOP','D:\software\scoop','User')
  ```

* **启用**

  ```powershell
  $env:SCOOP='D:\software\scoop'
  ```

* **install scoop**

  ```powershell
  iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
  ```

* **配置全局变量**

  ```powershell
  [environment]::setEnvironmentVariable('SCOOP_GLOBAL','D:\software','Machine')
  ```

* **启用**

  ```powershell
  $env:SCOOP_GLOBAL='D:\software'
  ```

## 使用

~~~powershell
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
~~~


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
scoop install 7zip
scoop install git
scoop install aria2
scoop install vim
scoop install gcc
scoop install nodejs
scoop install openjdk8-redhat
scoop install tomcat
scoop install another-redis-desktop-manager
scoop install ccleaner
scoop install copyQ
scoop install dbeaver
scoop install draw.io
scoop install filezilla
scoop install firefox
scoop install googlechrome
scoop install foobar2000
scoop install geekunintaller
scoop install idea-ultimate
scoop install clion
scoop install vscode
scoop install joplin
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

