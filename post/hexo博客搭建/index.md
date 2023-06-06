
**注：推荐观看bilibili的up主codesheep的hexo视频教程，本博客是参考该视频**

## 1. 部署环境

* **安装nodejs:** [官网下载](http://nodejs.cn/),下载完包括nodejs本身和npm包管理器

* 管理员身份**打开终端**(推荐git bash)

     `node -v`和`npm -v`验证是否安装成功

* 安装cnpm

     用npm安装cnpm的taobao源，防止丢包，提高安装成功率，用`cnpm -v`验证
     
     ```bash
     npm install cnpm -g --registry=https://registry.npm.taobao.org
     ```
     
     同样也可以直接修改npm的仓库源
     
     ```bash
     npm config set registry https://registry.npm.taobao.org
     #查看npm仓库
     npm config get registry
     ```
     
* 这里其实推荐使用nvm这个nodejs版本管理工具来安装nodejs

     ```bash
     curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
     wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
     
     nvm --version
     nvm ls-remote
     nvm install v12.18.0
     nvm ls
     ```

* 用 `cnpm install -g hexo-cli` 全局安装hexo博客框架，用`hexo -v`验证

## 2. 初始化博客

* 建立一个空的文件夹，作为blog博客目录

* 在blog目录下，`hexo init` 自动克隆博客框架到本地

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img349QHS.png)

* hexo s` 在本地`localhost:4000`端口可以预览本地的博客

## 3. 写博客

* `hexo n "blog name"` 生成新博文到source文件夹下，就可以愉快的写博客了
* `hexo clean` 清理目录之后，`hexo g` 更新生成

## 4. 部署博客到GitHub(免费，推荐)

* 为了远程访问，需要将博客部署到远程仓库，方式有多种，这里推荐github，因为免费

* github中新建仓库，仓库名设置为    `你的github用户名.github.io` 

* 在本地blog根目录安装git部署插件：`cnpm install --save hexo-deployer-git`

* 设置`_config.yml`文件，在文件最底部添加自己的Github仓库地址

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img34FTdf.png)

* ~~~
  hexo clean 
  hexo g
  hexo d     按照提示输入github密码完成远程部署
  ~~~

* 通过`你的github用户名.github.io` 远程访问自己的博客，完美打开

## 5. 后续自定义美化博客

* **主题选择**

  可以访问[hexo官方网站](https://hexo.io/themes/)，选择自己喜欢的主题

* 克隆主题到本地themes目录下：

  `git clone https://github.com/XXXX/XXX.git themes/XXX ` 

* 在`_config.yml` 中修改主题为XXX

* 最后在主题文件夹里面的配置文件里面实现自定义功能和美化
