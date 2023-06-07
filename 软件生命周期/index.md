# 软件生命周期


# 需求分析

* 概念：确定系统必须具有的功能和性能，系统要求的运行环境，并且预测系统发展的前景。

## 需求的获取

* 概念：软件需求的来源，软件工程师收集这些软件需求的方法
* 包含
  * **功能性需求**：用户和其他系统完成的功能，提供的服务
  * **非功能性需求**：必须遵循的标准，外部界面的细节，实现的约束条件，质量属性等等。
* 来源：用户目标，领域知识，投资者，运行环境，组织环境
* 获取方式：采访，设定情景，原型，会议，观察商业过程和工作流
* 需求获取面临的挑战：
  * 客户说不清需求
  * 需求易变性：在系统设计时，将软件的核心建筑放在稳定的需求上。
  * 问题的复杂度和对问题空间理解的不完备性与不一致性
* 需求诱导十原则：
  * 倾听
  * 有准备的沟通
  * 需要有人推动
  * 最好当面沟通
  * 记录所有决定
  * 保持同理协作
  * 聚焦并协调话题
  * 采用图形表示
  * 继续前进原则
  * 谈判双赢原则

## 需求确认

* 需求获取

* 需求提炼：对应用问题及环境的理解和分析，为问题涉及的信息，功能和系统行为建立**分析模型**，将用户需求精确化，完全化，最终形成下一步的需求规格说明书

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114202338.png)

* 需求描述（需求规格说明书）：对待开发系统的行为的完整描述，包含了功能性需求和非功能性需求，完成的标志是形成一份完整规范的需求规格说明书。

* 需求验证：对需求文档进行检查：有效性检查，一致性检查，完备性检查，现实性检查

## 需求变更

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114202345.png)

## 需求分析的任务

### 建立分析模型：面向过程，面向对象

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114204041.png)

* **面向过程分析模型**：用系统工程的思想和工程化的方法，根据用户至上的原则，自始自终按照结构化，模块化，自顶向下地对系统进行分析与设计。

  * 采用结构化分析方法（面向数据流进行需求分析的方法），用抽象模型的概念，按照软件内部数据传递，变换的关系，自顶向下逐层分解，知道找到满足功能要求的左右可实现的软件为止。
  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114204951.png)
  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114204955.png)
  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114211238.png)

* **面向对象分析模型**：由5个层次（主题层，对象类层，结构层，属性层，服务层）和5个活动（标识对象类，标识结构，定义主题，定义属性，定义服务）组成。

  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114212136.png)
  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114212139.png)

  * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114211243.png)

### 编写需求说明：用《需求规格说明书》规范的额形式准确地表达用户的需求

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114203028.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114203031.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114203034.png)

IEEE标准对需求文档提出了以下结构：

* 引言（需求文档目的，文档约定，预期读者和阅读建议，产品范围，参考文献）
* 综合描述（产品前景，产品功能与优先级，用户特征，运行环境，设计与实现上的限制，假设与依赖性）
* 需求描述（功能需求，数据需求，性能需求，外部接口，设计约束，软件质量属性，其他需求）
* 附录（词汇表，分析模型，特定问题列表）
* 索引

# 系统设计

* 概要设计：描述软件顶层架构和组织，划分不同组件
* 详细设计：详细描述各组件以便能够编码实现

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114214608.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114214611.png)

* 设计相关概念：
  * 抽象
  * 体系结构
  * 设计模式
  * 模块化
  * 信息隐藏
  * 功能独立
  * 精化
  * 重构

* **数据设计**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215052.png)

* **架构设计**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215505.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215409.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215410.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215411.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215412.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215413.png)

* **接口设计**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215730.png)

* **组件设计**

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114215733.png)

## 面向过程架构设计

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114221423.png)

变换型软件结构图

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114221224.png)

事务型软件结构图

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114221225.png)

混合型软件结构图

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114221226.png)

## 面向过程组件设计

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114222146.png)

## 面向对象架构设计

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114225316.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114225429.png)

## 面向对象组件设计（类设计和用例设计）

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114230312.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114230651.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114231017.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114231101.png)
