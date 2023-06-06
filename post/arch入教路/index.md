
我的linux入门是redhat系，跟着兄弟连用的centos。之后一直用的debian系，包括桌面日用，ubuntu，deepin，kubuntu，mint。早就听说了Arch邪教，纯命令安装系统，需要对linux系统有更深的了解，也借这个契机来试试。

## 第一步：虚拟机试探

1. 官网下载[安装包](https://archlinux.org/download/)

2. 启动vBox，开始进入安装界面

3. 确保开启虚拟机的EFI支持

4. 确保网络

   ```bash
   ip link
   #有线网络
   dhcpcd
   #无线网络
   iwctl
   [iwd] device list
   [iwd] station device scan
   [iwd] station device get-networks
   [iwd] station device connect SSID
   
   ping www.baidu.com
   ```

5. 更新系统时间

   ```bash
   timedatectl set-ntp true
   #检查服务状态
   timedatectl status
   ```

6. 系统分区

   ```bash
   fdisk -l  
   cfdisk /dev/sdx  (x替换成相应的硬盘字母，一般是a或b，默认gpt即可)
   ```

   分区结果

   ![image-20210318123200700](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210318123200700.png)

   ```bash
   mkfs.fat -F32 /dev/sda1
   mkfs.ext4 /dev/sda3
   mkswap /dev/sda2
   swapon /dev/sda2
   ```

7. 挂载

   ```bash
   mount /dev/sda3 /mnt
   mkdir /mnt/boot
   mount /dev/sda1 /mnt/boot
   ```

8. 安装

   ```bash
   vim /etc/pacman.d/mirrorlist
   # 找到中国镜像移到最上面或者添加
   Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
   # pacstrap脚本安装arch基础
   pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim
   ```

9. 生成fstab挂载文件

   ```bash
   genfstab -U /mnt >> /mnt/etc/fstab
   cat /mnt/etc/fstab
   ```

10. 切换根目录，进入安装好的系统

    ```bash
    arch-chroot /mnt
    ```

11. 本地化设置

    ```bash
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
    hwclock --systohc
    
    vim /etc/locale.gen
    en_US.UTF-8 UTF-8
    zh_CN.UTF-8 UTF-8
    zh_TW.UTF-8 UTF-8
    locale-gen
    
    vim /etc/locale.conf
    LANG=en_US.UTF-8
    
    vim /etc/hostname
    jack1024
    
    vim /etc/hosts
    127.0.0.1	localhost
    ::1		localhost
    127.0.1.1	jack1024.localdomain	jack1024
    
    #设置root密码
    passwd
    ```

12. 设置微码引导

    ```bash
    #intel CPU
    pacman -S intel-ucode
    #AMD CPU
    pacman -S amd-ucode
    ```
    
13. 安装引导

    ```bash
    pacman -S os-prober ntfs-3g grub efibootmgr
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    
    #生成并查看引导配置
    grub-mkconfig -o /boot/grub/grub.cfg
    cat /boot/grub/grub.cfg
    
    # 重启
    umount -R /mnt
    reboot
    ```

    ![image-20210318131450261](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210318131450261.png)

    出现这个说明安装一切搞定，重启后进入邪教生活。

14. 常用软件

    ```bash
    #重启后登录
    jack1024 login: root
    password: ****
    #联网
    dhcpd
    ping www.baidu.com
    systemctl enable dhcpcd
    
    #配置国内源
    vim /etc/pacman.conf
    #文档末尾添加
    [multilib]
    Include = /etc/pacman.d/mirrorlist
    [archlinuxcn]
    Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
    #安装 archlinuxcn-keyring 包以导入 GPG key
    pacman -S archlinuxcn-keyring
    
    #更新
    pacman -Syyu
    
    #安装常用软件
    pacman -S openssh man git
    systemctl start sshd
    systemctl enable ssdd.service
    
    #添加用户
    useradd -m -G wheel -s /bin/bash username
    passwd username
    
    #给用户sudo权利
    pacman -S sudo
    #第一种方法
    su - root
    chmod u+w /etc/sudoers
    vim /etc/sudoers
    #找到这行 root ALL=(ALL) ALL,在他下面添加xxx ALL=(ALL) ALL (这里的xxx是你的用户名)
    chmod u-w /etc/sudoers
    #第二种方法
    ln -s /usr/bin/vim /usr/bin/vi
    visudo
    %wheel ALL=(ALL) ALL
    
    #安装zsh
    #不知道为什么虚拟机无法从github clone，所以采用gitee
    sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

    # 然后参考之前的linux工作环境配置那篇博客自定义zsh
    
    # 由于虚拟机暂时无法访问github，需要通过gitee安装一些插件
    git clone https://gitee.com/mirror-github/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    
    git clone https://gitee.com/lxgyChen/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
    
    git clone https://gitee.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
    ```

    ```bash
    # 经过多次试错终于能让虚拟机从github拉取项目了
    用浏览器访问 IPAddress.com 使用 IP Lookup 工具获得
    github.com
    github.global.ssl.fastly.net
    raw.githubusercontent.com
    三个域名的ip地址
    然后写入/etc/hosts中即可
    
    ```

    

    ![image-20210318160332058](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210318160332058.png)

15. 安装图形界面

    ```bash
    #安装声卡驱动
    sudo pacman -S alsa-utils pulseaudio pulseaudio-alsa
    
    #查看显卡型号
    lspci | grep VSA
    #查看独显型号
    lspci | grep 3D
    #参考官网驱动表进行安装
    sudo pacman -S 驱动包名
    
    #安装输入设备驱动
    pacman -S xf86-input-libinput
    
    #安装笔记本触摸板驱动
    sudo pacman -S xf86-input-synaptics
    
    #安装中文字体
    sudo pacman -S ttf-dejavu wqy-bitmapfont wqy-microhei wqy-zenhei noto-fonts noto-fonts-emoji
    
    # xorg用于实现窗口系统，sddm是登陆管理器，登陆管理器还有lightdm等等，但是个人比较推荐sddm，sddm可以支持多个桌面环境的切换。
    pacman -S xorg xorg-server
    #安装kde桌面
    pacman -S plasma
    #安装kde应用
    pacman -S kde-applications
    #安装sddm图形登录界面
    sudo pacman -S sddm sddm-kcm
    systemctl enable sddm
    
    #安装网络管理
    sudo pacman -S net-tools networkmanager
    sudo systemctl enable NetworkManager
    ```
    
16. virtualbox安装增强功能（经过反复折腾，最终安装失败）

    ```bash
    sudo pacman
    sudo pacman -S linux-headers
    sudo pacman -S base-devel
    #理论上安装以上就行了，但是还是失败了，不知道为什么
    ```


## 安装主步骤：

1. 联网
2. 更新时间
3. 创建分区
4. 格式化挂载点
5. 挂载分区
6. 国内镜像置顶
7. 拉取安装件（包括devel）
8. 生成挂载文件
9. 切换根目录
10. 调整时区
11. 生成配置文件
12. 本地化修改
13. 修改root密码
14. 网络工具安装
15. 安装微码引导

## pacman包管理

* 简洁高效
* 拥有神器：Arch User Repository

```bash
# 查看本地软件仓库更新情况
pacman -Sy
# 强行更新本地软件仓库
pacman -Syy
# 从本地仓库更新软件
pacman -Su
# 优雅の一步：同步并更新所有
pacman -Syu

#滚动更新
pacman -Syyu

# 安装软件
pacman -S 软件名
# 通过正则表达式搜索软件
pacman -Ss 正则表达式
# 清除本地软件仓库
pacman -Sc
# 完全清空包缓存（Warning: 关于pacman -Scc，仅在你确定不需要做任何软件包降级工作时才这样做。pacman -Scc会从缓存中删除所有软件包）
pacman Scc

# 仅仅删除软件本身（不推荐）
pacman -R 软件名
# 删除软件、它的依赖（推荐）
pacman -Rs 软件名
# 删除软件、它的依赖、全局配置文件，本地配置文件不会删除（推荐）
pacman -Rns 软件名

# 列出已安装软件包
pacman -Q
# 列出自己安装的软件
pacman -Qe
# 列出安装的软件（只有名字）
pacman -Qq
# 通过正则查询本地安装的软件
pacman -Qs 正则表达式
# 列出所有孤儿包
pacman -Qdt
# 删除所有孤儿包
pacman -R $(pacman -Qdtq)
# 查新哪些包拥有指定文件
pacman -Qo 文件名
```

### AUR助手

* Arch 用户软件仓库 通常称为 AUR，是给 Arch 用户的基于社区的软件存储库。

* 什么是 AUR（Arch 用户软件仓库）？

  Arch 用户软件仓库 通常称为 AUR，是给 Arch 用户的基于社区的软件存储库。

  根据软件包在 AUR 社区的流行程度，用户编译的软件包会进入到 Arch 的官方存储库。

* 什么是 AUR 助手？

  AUR 助手是一个包装程序，允许用户从 AUR 存储库安装软件包，而无需手动干预。

  很多用例实现了自动化，比如包搜索、解决依赖关系、检索和构建 AUR 包、Web 内容检索和 AUR 包提交之类。

```bash
# Yay 是 Arch Linux 下基于 CLI 的最佳 AUR 助手，使用 Go 语言编写。Yay 是基于 yaourt、apacman 和 pacaur 设计的。
# 这是最合适推荐给新手的 AUR 助手。类似于 Pacman，其使用方法和 pacman 中的命令和选项很相似，可以让用户在搜索过程中找到匹配的软件包提供程序，并进行选择。

git clone https://aur.archlinux.org/yay.git
cd yay-git
makepkg -si

# 然后就可以直接使用yay命令了，和pacman几乎一致的命令
```

用debian系的小伙都惊呆了，AUR是真的牛皮

![image-20210318212110059](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210318212110059.png)