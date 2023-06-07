# hexo博客优化之图床迁移&google收录


前言：

* 搭建完hexo后，当然就是写博客，markdown的语法插入图片很简单，但是把大量图片放在博客文件里面，未免太臃肿，会大大拖慢博客的打开时间（更别说我的博客是托管在github上的了）。**图床**就是一个好的解决方案，顾名思义，图床就是一个存储图片个“床”，一个**云端的图片库**，它把博客和图片分离开来，更好管理的同时也不会拖慢博客的速度。
* 我写博客虽然都是给自己看的，但是搜索引擎搜不到自己的文章总感觉缺了点什么:laughing:,让谷歌收录自己的博文，再加上自己博文的质量，就能在google搜索引擎上根据关键词搜索到自己的文章了。（百度的收录我没有弄，不过大同小异）

# 图床迁移：

**我之前的图床用的是[路过图床](https://imgchr.com/)，这是个免费的图床，还不错，免费额度完全够用，但是感觉图片打开速度有点慢。所以就百度了一下，发现了一种同样免费且快捷的方式：gitee(码云)+PicGo。迁移过后，实测效果明显，下面就来总结一下**

* 现在码云建立一个仓库，按照下面格式来。没有账号的自己注册。

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220522094836.png" style="zoom: 67%;" />

* 在gitee的**个人设置**里选择   **私人令牌**——>**生成新令牌**，最后会生成一串密文，保存它，后面和PicGo绑定要用。

* 下载PicGo软件[下载链接](https://github.com/Molunerfinn/PicGo/releases)，Windows系统选择exe安装程序下载，macos选择dmg程序安装程序下载。

* 进入PicGo界面，插件搜索gitee-uploader，安装，然后重启。

* 进入图床设置，选择gitee，然后填入以下信息

  * repo：gitee中你建立的仓库地址，例如`yangfanjack/image`
  * branch：填`master`
  * token：刚才的密文
  * path：一般填`img`
  * customPath：不填
  * customUrl：不填

* 然后点击**设为默认图床**——>**确认**，即可，enjoy it!

# Google收录：

前提：看到这里的我相信都有办法进google吧

* 进入[Google Search Console](https://www.google.com/webmasters/#?modal_active=none)，既然有新的，就选择左边的网域，按照他的格式例如`ordinaryman.club`填入你的域名，点击继续

* 会DNS验证，去你的域名购买商添加一条txt record，如果之前更改过DNS服务器地址，就去DNS代理商添加record，我是cloudflare代理的，所以以此为例：

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200311215859.png)

  点击**+Add record**，然后**type**选择TXT，**Name**填@，**Content**就填Google给你的txt内容，点击save，完成。

* 返回Google Search Console界面，点击验证，一般会过一段时间通过，不过我是马上就通过了:neutral_face:。

* 然后进入你的本地博客根目录，安装站点地图插件：

  ```shell
  npm install hexo-generator-sitemap --save
  ```

  安装完后进入根目录的配置文件_config.yml

  * 在URL下：

  ```shell
  url: http://XXXXXX  //写入你的域名
  ```

  * 在最后加上：

  ```shell
  sitemap:
  	path: sitemap.xml
  ```

  保存后，hexo三件套：`hexo clean`       `hexo g`      `hexo d`，访问网页：`你的域名/sitemap.xml`，出现以下页面说明配置成功

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200311215929.png)

* 进入Google Search Console的你的域名管理页面，点击站点地图，添加你的站点地图的网址：`你的域名/sitemap.xml`，等待一天时间，就可以生效啦。
