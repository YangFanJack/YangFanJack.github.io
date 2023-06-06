
rpm打包需要一个独立的打包目录

* BUILD：编译rpm包的临时目录
* BUILDROOT：编译后生成的软件临时安装目录
* RPMS：最终生成的可安装rpm包存放目录
* SOURCES：所有源文件和补丁文件存放目录
* SPECS：存放SPEC文件的目录
* SRPMS：软件最终的rpm源码格式存放路径

打包需要从源码sources开始，将存放源码的目录打包压缩成tar.gz格式，放进SOURCES目录中

然后在SPECS目录中创建并编辑spec文件

最后根据spec文件进行打包`rpm -ba rpmbuild/SPECS/xxx.spec`
