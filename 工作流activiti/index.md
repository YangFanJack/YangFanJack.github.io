# 工作流Activiti


工作流的作用是：对系统的业务流程进行自动化管理。一个软件的系统核心是系统的业务流程，工作流只是协助进行业务流程管理。工作流能更好的管理业务流程，提高系统的可扩展性。

工作流的具体应用：

* 关键业务流程
* 行政管理类
* 财务相关类
* 客户服务类
* 特殊服务类 

在没有专门的**工作流引擎**前，要实现流程控制，通常的做法是采用状态字段的值类跟踪流程的变化情况。这样不用角色的用户，通过状态字段的取值来决定记录是否显示。但是这样做和业务代码的耦合性特别高。

## Activiti7概述

Activiti是一个工作流引擎，activiti可以将业务系统中复杂的业务流程抽取出来，使用专门的建模语言BPMN2.0进行定义，业务流程按照预先定义的流程进行执行。实现了系统的流程由activiti进行管理，**减少业务系统由于流程变更进行系统升级改造的工作量**，从而提高系统的健壮性，同时减少了系统开发维护成本。

**BPM**：Business Process Management业务流程管理

**BPM软件**：对企业内部及外部的业务流程的整个生命周期进行建模、自动化、管理监控和优化，使企业成本降低，利润得以大幅提升。

**BPMN**：Business Process Model And Notation业务流程模型和符号，是由BPMI开发的一套标准的业务流程建模符号，使用BPMN提供的符号可以创建业务流程。

![image-20210923203846548](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210923203846548.png)

BPMN图形其实是通过xml表示的业务逻辑，上面的.bpmn文件使用文本编辑器打开后是一个xml文件

## Activiti的使用

### Activiti的使用步骤

**部署activiti**

Activiti是一个工作流引擎，业务系统访问activit的api接口，就能方便的操作流程相关数据，这样就可以把工作流环境与业务系统的环境集成在一起。

**流程定义**

使用activiti流程建模工具（activiti-designer）或者通过手写xml文件来定义业务流程（.bpmn文件）

**流程定义部署**

使用activiti提供的api把流程定义内容存储起来，在Activiti执行过程中可以查询定义的内容

Activiti执行把流程定义存储在数据库中

**启动一个流程实例**

启动一个流程实例表示开始一次业务流程的运行

For Example：在员工请假流程定义部署完成后，如果张三要请假就可以启动一个流程实例，如果李四要请假也启动一个流程实例，两个流程的执行互不干扰

**用户查询待办任务Task**

因为系统的业务流程已经交给了activiti管理，通过activiti就可以查询当前流程执行到哪了，当前用户需要办理什么任务了，这些东西activiti都帮我管理了

**用户办理任务**

用户查询待办任务后，就可以办理某个任务了，如果这个任务办理完成后还需要其他用户办理，比如：采购单创建后由部门经理审核，这些过程也是由activiti帮我们完成了。

**流程结束**

当任务办理完成后没有下一个任务节点了，这个流程实例就完成了

### Activiti环境

```xml
<!-- https://mvnrepository.com/artifact/org.activiti/activiti-engine -->
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-engine</artifactId>
    <version>7.1.0.M6</version>
</dependency>
```

Activiti运行需要有数据库的支持，支持的数据库有：h2，**mysql**，oracle，postgres等。

### 在IDEA中创建一个Activiti的Maven项目框架

* 导入Maven依赖包：
  * Activiti相关的包：activiti-engine包，activiti依赖的jar包，activiti依赖的spring包
  * mysql的驱动包：mysql的数据库驱动包
  * mybatis的包：
  * log4j的包
  * 数据库连接池的包：第三方数据库连接池包dbcp
  * 单元测试包junit   

