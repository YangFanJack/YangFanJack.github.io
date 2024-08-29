# 实体机安装Arch


## 准备：

1. 机器：华硕飞行堡垒fx-60vm

2. 第一系统：win10

3. Arch镜像

   > https://archlinux.org/download/

4. 镜像考录工具：Rufus

5. U盘

## 安装系统

#### 联网

```bash
#调整键盘映射
ls /usr/share/kbd/keymaps/**/*.map.gz
loadkeys mac-us
#调整字体
ls /usr/share/kbd/consolefonts
setfont ter-132n
setfont ter-122b

#验证引导模式 UEFI
ls /sys/firmware/efi/efivars

#查看网卡
ip link
#有线连接
dhcpcd

#无线连接
#查看wifi是否关闭，开启wifi
rfkill list
rfkill unblock wifi
#wifi联网
iwctl
[iwd] device list
[iwd] station wlan0 scan
[iwd] station wlan0 get-networks
[iwd] station wlan0 connect SSID
#分配ip
dhcpcd

#测试网络连接
ping archlinux.org
```

#### 更新系统时间

```bash
#更新系统时间(NTP 或网络时间协议用于通过网络同步计算机系统时钟)
timedatectl set-ntp true
#检查服务状态
timedatectl status
```

#### 系统分区

```bash
#列出所有可用的块设备(sdX / nvme0nX)
lsblk
#查看每个磁盘的分区情况
fdisk -l
fdisk -l /dev/sdX
#新建分区(选择GPT)
cfdisk /dev/sdX  (X替换成相应的硬盘字母，一般是a或b，默认gpt即可)

#分区建议
# /boot/efi:和win10共用 512M   EFI System
# swap:固态硬盘1G   Linux swap
# /:固态硬盘剩下所有   Linux filesystem

#EXT
#格式化分区
mkfs.fat -F32 /dev/sda1 #/boot/efi
mkfs.ext4 /dev/sda2 #/
mkswap /dev/sda3 #交换分区
swapon /dev/sda3
#挂载
mount /dev/sda2 /mnt
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi

#Btrfs
#格式化分区
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.btrfs -f -m single -L Arch /dev/nvme0n1p2
mkswap /dev/nvme0n1p3
swapon /dev/nvme0n1p3
#建立子卷
mount -t btrfs -o compress=lzo /dev/nvme0n1p2 /mnt
btrfs subvol create /mnt/@
btrfs subvol create /mnt/@home
btrfs subvol create /mnt/@cache
btrfs subvol create /mnt/@log
btrfs subvol create /mnt/@tmp
# 使用 chattr 忽略无需写时复制的目录
chattr +C /mnt/@cache
chattr +C /mnt/@log
umount /mnt

#挂载
mount -o noatime,nodiratime,ssd,compress=zstd,subvol=@ /dev/nvme0n1p2 /mnt
mkdir -p /mnt/{boot/efi,home,var/{log,cache}}
mount -o noatime,nodiratime,ssd,compress=zstd,subvol=@home /dev/nvme0n1p2 /mnt/home
mount -o noatime,nodiratime,ssd,compress=zstd,subvol=@log /dev/nvme0n1p2 /mnt/var/log
mount -o noatime,nodiratime,ssd,compress=zstd,subvol=@cache /dev/nvme0n1p2 /mnt/var/cache
mount -o noatime,nodiratime,ssd,compress=zstd,subvol=@tmp /dev/nvme0n1p2 /mnt/var/tmp
mount /dev/nvme0n1p1 /mnt/boot/efi

# 安装Btrfs快照工具
yay -S timeshift timeshift-autosnap grub-btrfs

```

#### 安装arch核心

```bash
# 可以直接使用reflector命令检索排序镜像并保存到mirrorlist里
reflector --country China --age 24 --sort rate --protocol https --save /etc/pacman.d/mirrorlist

# 或者手动更改
vim /etc/pacman.d/mirrorlist
#找到中国镜像移到最上面或者添加
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
#更新(保持软件包仓库数据库的最新状态)
pacman -Syy
#pacstrap脚本安装arch基础
pacstrap /mnt base base-devel linux linux-firmware linux-headers dhcpcd vim networkmanager intel-ucode git openssh bash-completion ntfs-3g sudo btrfs-progs
```

#### 生成fstab挂载文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

#### 进入ch-root

```bash
# 从实时环境进入新安装的系统
arch-chroot /mnt
#更新(保持软件包仓库数据库的最新状态)
pacman -Syy
#配置默认时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
#设置可用本地化语言
vim /etc/locale.gen
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8

locale-gen
#设置默认语言
vim /etc/locale.conf
LANG=en_US.UTF-8
# LANG=zh_CN.UTF-8
#本地化设置
vim /etc/hostname
jacksArch

vim /etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.1.1	jacksArch.localdomain	jacksArch

#自启动设置
systemctl enable NetworkManager sshd

#设置root密码
passwd

#新增普通用户
useradd -m -G wheel -s /bin/bash jack1024
passwd jack1024
#给用户sudo权利
ln -s /usr/bin/vim /usr/bin/vi
visudo
%wheel ALL=(ALL) ALL
```

#### 安装引导

```bash
pacman -S grub efibootmgr efivar
# 如果与其他操作系统一起安装，在/etc/default/grub文件中注释#GRUB_DISABLE_OS_PROBER=false
pacman -S os-prober

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

#生成并查看引导配置
grub-mkconfig -o /boot/grub/grub.cfg
cat /boot/grub/grub.cfg

# 对于 btrfs 文件系统，需要编辑 mkinitcpio 文件，通常位于 /etc/mkinitcpio.conf，找到 MODULES=() 一行，在括号中添加 btrfs，这是为了在系统启动时提前加载 btrfs 内核模块，从而正常启动系统。记得每次编辑完 mkinitcpio 文件后都需要手动重新生成 initramfs
vim /etc/mkinitcpio.conf
MODULES=(btrfs)
HOOKS=(base udev autodetect modconf block filesystems keyboard btrfs)
mkinitcpio -P

#退出chroot
exit
#重启
umount -R /mnt/boot/efi
umount -R /mnt
reboot
```

## 新系统完善和配置

#### 网络配置

```bash
#调节字体(在该目录下选择一个字体)
setfont /usr/share/kbd/consolefonts/sun12x22.psfu.gz
#联网
nmcli device wifi list
nmcli device wifi connect yang password XXXXX
#查看网络配置
cat/etc/pacman.d/mirrorlist
#网络配置
vim /etc/pacman.conf
#打开Color和TotalDownload注释
#文档末尾添加
[multilib]
Include = /etc/pacman.d/mirrorlist
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

#更新所有软件
sudo pacman -Syyu

#安装 archlinuxcn-keyring 包以导入 GPG key
sudo pacman -S archlinuxcn-keyring

#安装aur管理工具yay
sudo pacman -S yay

# 通过环境变量进行命令行代理
export https_proxy=http://127.0.0.1:8889
export http_proxy=http://127.0.0.1:8889
export all_proxy=socks5://127.0.0.1:1089
```

```bash
#如果安装keyring出错
sudo rm -rf /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman -S archlinuxcn-keyring
```

#### 图形化界面

```bash
#窗口系统服务xorg 或者 wayland
sudo pacman -S xorg xorg-server
sudo pacman -S plasma-wayland-session xdg-desktop-portal-kde
#kde
sudo pacman -S plasma
#不建议kde-applications，太多，选几个就行了
sudo pacman -S ark dolphin dolphin-plugins juk kdeconnect kdenlive konsole okular
#登录界面sddm
sudo pacman -S sddm sddm-kcm
systemctl enable sddm
#sddm中文界面
sudo SYSTEMD_EDITOR=vim systemctl edit sddm

[Service]
Environment=LANG=zh_CN.UTF-8

#xfce
sudo pacman -S xfce4 xfce4-goodies
#lightdm
sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
sudo systemctl enable lightdm
```

#### 字体

```bash
sudo pacman -S ttf-dejavu wqy-bitmapfont wqy-microhei wqy-zenhei noto-fonts noto-fonts-emoji
#默认语言
vim .xprofile
export LC_ALL=zh_CN.UTF-8
```

#### 声音&显卡&蓝牙

```bash
sudo pacman -S alsa-utils pulseaudio pulseaudio-alsa
sudo pacman -S wireplumber pipewire-pulse
sudo pacman -S alsa-firmware sof-firmware alsa-ucm-conf

//sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-settings
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel
sudo pacman -S xf86-video-intel intel-media-driver
sudo pacmna -S bluez bluez-utils bluedevil pulseaudio-bluetooth
systemctl enable bluetooth
```

#### 电源管理

```bash
# tlp提供优秀的linux高级电源管理功能，配置文件位于/etc/default/tlp
sudo pacman -S tlp tlp-rdw
# 在您使用(tlp-rdw)之前需要使用NetworkManager并且需要启用 NetworkManager-dispatcher.service
sudo systemctl enable NetworkManager-dispatcher.service
sudo systemctl enable tlp.service tlp-sleep.service
# 屏蔽systemd服务systemd-rfkill.service和systemd-rfkill.socket来防止冲突
sudo systemctl mask systemd-rfkill.service systemd-rfkill.socket
sudo tlp start
# 只对thinkpad有用的功能,充电阈值控制以及电池校准
sudo pacman -S tp_smapi acpi_call
```

#### 输入法

```bash
yay -S fcitx5-im fcitx5-rime fcitx5-chinese-addons fcitx5-pinyin-zhwiki fcitx5-pinyin-moegirl fcitx5-pinyin-zhwiki-rime fcitx5-pinyin-moegirl-rime

#开机自启
cd ~/.config
mkdir autostart
cd autostart
sudo mv /usr/share/applications/fcitx5.desktop ./
#右下脚输入法右键配置，删掉拼音加入中州韵

#环境变量
vim ~/.pam_environment
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE  DEFAULT=fcitx
XMODIFIERS    DEFAULT=\@im=fcitx
SDL_IM_MODULE DEFAULT=fcitx

#词库
yay -S rime-ice-git
#皮肤
sudo pacman -S fcitx5-material-color
```

#### 触摸板
```bash
yay -S xf86-input-libinput
yay -S xorg-xinput
yay -S xf86-input-evdev xf86-input-elographics
```

## 需要注意的点

```bash
#新版本的arch在grub中默认没有windows的探测，需要在/etc/default/grub中加上
GRUB_DISABLE_OS_PROBER=false
#然后重新grub-mkconfig
grub-mkconfig -o /boot/grub/grub.cfg
```

```bash
#安装mysql
pacman -S mysql57
#初始化数据库
sudo mysqld --initialize --user=mysql
#启动mysql
sudo systemctl start mysqld
#登录mysql
mysql -uroot -p
#修改密码
mysql> use mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'your-password';
#查看mysql默认配置
mysqld --verbose --help
#默认配置文件:/etc/mysql/my.cnf
#默认数据库文件夹:/var/lib/mysql/

```

```bash
#安装v2raya
yay -S v2raya v2ray
systemctl enable --now v2raya
```

```bash
#安装libreoffice
sudo pacman -S libreoffice-fresh libreoffice-fresh-zh-cn
```

```bash
# 安装nvm
yay -S nvm
nvm --version
nvm ls-remote
nvm install v12.18.0
nvm ls
npm install cnpm -g --registry=https://registry.npm.taobao.org

# 安装zsh
yay -S zsh
chsh -s /bin/zsh
yay -S oh-my-zsh-git
cp /usr/share/oh-my-zsh/zshrc ~/.zshrc

echo 'source /usr/share/nvm/init-nvm.sh' >> ~/.bashrc
echo 'source /usr/share/nvm/init-nvm.sh' >> ~/.zshrc

# 添加nvm环境变量到.zshrc
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

# zsh速度优化
export NVM_DIR="$HOME/.nvm"
[[ -s "$NVM_DIR/nvm.sh" ]] && source "$NVM_DIR/nvm.sh" --no-use

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k

plugins=(其他的插件 zsh-autosuggestions zsh-syntax-highlighting)
HIST_STAMPS="yyyy-mm-dd"
ZSH_THEME="powerlevel10k/powerlevel10k"
# 自定义主题
p10k configure
```

## pacman命令
```bash
# 安装
pacman -S xxx
# 删除
pacman -Rns xxx
# 删除孤儿包
sudo pacman -Qdtq | pacman -Rs -
# 升级软件包
pacman -Syu
# 搜素
pacman -Ss xxx
# 查询某个包是否已经安装
pacman -Qs xxx
```
