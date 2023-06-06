
## tomcat服务器的三种部署方式：

1. 直接将项目放进webapps目录下即可

   * `/项目名`就是访问路径

   * 可以简化部署：将项目里的全部文件打一个war包，然后把war包放进webapps里面会自动解压缩。删除war包会自动删除项目。

    ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200319121227.png)

    ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200319115144.png)

2. `conf/server.xml`配置文件中，加上`<Context docBase=项目地址 path=虚拟访问路径 />`

   * 更加灵活些，范文路径是path，不一定是目录名
   * 但是修改server.xml，不是很安全

3. 热部署：在/conf/Catalina/localhost下面创建任意名称的xml文件，里面加上`<Context docBase=项目地址 />`，范文路径就是xml文件的名称

## IDEA中的tomcat

* 与eclipse或则myeclipse那样直接部署就在在tomcat的安装文件夹里生成部署文件不同，IDEA会为每一个tomcat部署的Web项目单独建立一份相关文件，里面包含配置文件，日志和临时目录（如下图），在自己的家目录下：`C:\Users\你的用户名\.IntelliJIdea2019.3\system\tomcat\`

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200319120803.png)

* **重点：** 这里的文件对应着tomcat安装根目录下的对应文件，conf目录下是配置文件，idea默认就是热部署，就是在这里面对应的位置生成xml文件。work目录里面是临时文件，包含服务器端jsp被转化为servlet的java代码文件和class字节码文件。

* tomcat服务器真正访问的是**out目录**(tomcat部署的web项目)，而out目录下的部署的项目对应着**web工作空间目录**下的所有资源，idea的out目录和web目录都在自己创建的项目地址下。

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20200319123804.png)

* WEB-INF目录下有个classes目录，里面的java字节码文件是从src目录导过来的。

* WEB-INF目录是个安全目录，其下的资源不能直接被浏览器访问