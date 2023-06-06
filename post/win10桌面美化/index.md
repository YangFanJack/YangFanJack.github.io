
* **先放上一张我的桌面：**

* **前言：Windows操作系统经过几十年的发展，到目前已经相当成熟，在个人桌面操作系统中也是处于霸主地位，但是对于我而言，如果根据自我审美和操作习惯对Win10进行美化，无疑也是会提高舒适度和办公学习效率的。**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232639.png)

# everything+Wox

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232658.png" style="zoom: 50%;" />

* 这东西能让你的桌面干干净净，再也不用在桌面码满快捷方式，还能一定程度上摆脱鼠标的束缚，查找软件，文档，甚至网页都是可以的，还有很多功能我没有探索到的。
* 其实关于全局搜索软件的尝试，我最开始是用的listary，很好用，就是有个缺点，能改样式的高级版收费。所以我就换了Wox，注意：要让Wox能全局搜索，需要配合everything，两个软件都设置成开机自启，美滋滋。

# RocketDock

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232708.png)

* win10的任务栏和Mac、Linux的Dock栏的喜爱见仁见智，不过我觉得Mac的dock栏的设计确实更加具有美感，效率性很不差。经过我的一番搜索，其实有很多：ObjectDock，BitDock等等，一些桌面第三方都支持这一功能，我为什么选择RocketDock呢？一个字，小。RocketDock对内存的占用几乎可以忽略不计，开机自启后完全不会拖慢Windows系统的运行。

* [官网下载](https://punklabs.com/)，安装后可以在设置界面进行自定义设置，选项很多，这里不一一介绍了，有一点需要注意的是，我在选择了他的“将窗口最小化到Dock栏”这一项时，有部分软件并不能最小化到Dock栏，还是在任务栏最小化，经过我的分析，发现在Program Files和Program Files (x86)目录下的软件的反应不同，我感觉可能是因为该软件停更了几年，对64位的支持不好？也有可能是我的个别情况。

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232720.png" style="zoom:67%;" />

* 要把图标加到dock栏由两种方法：

  1. 直接把快捷方式或者exe文件拖入dock栏
  2. 右击dock栏，新建项目——>文件，然后右击新生成的文件图标，可以选择文件位置和图标

* 关于图标的自定义，这里推荐一个网站：[阿里巴巴矢量图标库](https://www.iconfont.cn/)

* 如果要有如下stacks docklet的效果，需要下载一个插件，但是官网关闭了，如果百度不到的可以联系我。

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232727.png" style="zoom: 67%;" />

# Win10任务栏调整

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232735.png)

* Windows的任务栏是win的精华，并不能被dock完全取代，我是把它放在顶部。**右击任务栏**——>**任务栏设置**，里面有很多自定义选项，根据需要调整

* 我不习惯Win10的原始开始菜单，借助了**"腾讯电脑管家"**的工具箱里面的**"经典开始菜单"**工具，可以换成经典模式

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521232743.png)

**合理利用Windows的键盘快捷键可以极大的提高效率，同时也会让你的系统更加的规范**

**ok，这篇博文就先介绍这几个最基本的工具，如果有什么不清楚的或者讨论的请留言，或者联系我的QQ（在菜单关于里面）**

2023年，win10变成win11，win最应该优化的其实应该是终端体验，有两种方案，一种是安装子系统，另一种是安装兼容层，前者类似wsl，后者类似msys2。体验后最舒服的还是git-bash + scoop包管理这个组合，实用且轻量。
linux shell中，zsh是比较不错的，windows的git-bash能用吗？当然:
1. 安装zsh命令到git-bash中：`https://packages.msys2.org/package/zsh?repo=msys&variant=x86_64`
2. 使用peazip软件解压缩，覆盖到git根目录
3. 在家目录下的.bashrc中加入：
```bash
if [ -t 1 ]; then
  exec zsh
fi
```
4. 安装oh-my-zsh，和linux一样，不再赘述