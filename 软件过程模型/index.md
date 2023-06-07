# 软件过程模型


# 软件工程

* ## 三要素：

  * 方法：软件工程方法为软件开发提供了“如何做”的技术。软件工程方法分为两类：结构化方法和面向对象方法。

  * 工具：软件工具为软件工程方法提供了自动的或半自动的软件支撑环境。集成的软件工程工具再加上人的因素构成了软件工程环境。
  * 过程：软件工程的过程则是将软件工程的方法和工具综合起来以达到合理、及时地进行计算机软件开发的目的。过程定义了方法使用的顺序、要求交付的文档资料、为保证质量和协调变化所需要的管理、及软件开发各个阶段完成的里程碑。

* ## 七原则

  * **用分阶段的生命周期计划严格管理**
  * **坚持进行阶段评审**
  * **实行严格的产品控制** 
  * **用现代程序设计技术** 
  * **结果应能清楚地审查**
  * **开发小组的人员应该少而精**
  * **承认不断改进软件工程实践的必要性**

* ## 知识体系

  * ### 开发与维护过程

    * 软件需求
    * 软件设计
    * 软件构造
    * 软件测试
    * 软件维护

  * ### 支持组织过程

    * 软件配置管理
    * 软件工程管理
    * 软件过程
    * 软件工具（编码，测试等）
    * 软件质量

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114151251.png)

# 软件过程（生命周期）

## 	软件生命周期：

* 问题定义：项目计划报告
* 可行性研究：可行性研究报告
* 需求分析：需求规格说明书
* 概要设计：概要设计说明书
* 详细设计：详细设计说明书
* 编码：源程序
* 测试：软件测试报告
* 维护：软件维护说明

## 软件过程模型/软件生存周期模型

### 传统软件过程模型

#### 1. 瀑布模型：适用于系统需求明确且稳定，技术成熟，工程管理较严格的场合，如军工，航天，医疗。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114152356.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114152903.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114152908.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114152943.png)

#### 2. V模型

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114152915.png)

#### 3. 原型模型（当客户不清楚系统的具体输入输出；或开发者不确定算法的效率，软件与操作系统是否兼容以及客户与计算机交互的方式时，使用原型模型）

原型：一个部分开发的产品，是客户和开发人员能够对加护开发的系统的相关方面进行检查。

原型化的目的：

* 明确并完善需求
* 研究技术选择方案

原型结果：

* 抛弃原型
* 把原型发展为最终产品

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114153457.png)

#### 4. 增量模型（适用于软件开发过程中可能发生变化，具有较大风险，或者希望尽早进入市场的项目）

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114154131.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114154135.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114154139.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114154143.png)

#### 5. 螺旋模型(适用于需求不明确或需求可能发生变化的大型复杂软件系统)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155035.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155039.png)

#### 6. 喷泉模型

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155043.png)

### 现代软件过程模型

#### 1. 基于构件的开发模型（使用于系统之间有共性的情况）

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155829.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155832.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114155836.png)

#### 2. 统一过程模型

* 由Rational公司推出的完整且完美的软件工程方法

* 基于面向对象方法学

* 使用统一建模语言UML

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114160438.png)

* 实践视角（6条最佳实践）
  * 迭代式开发
  * 管理需求
  * 基于构件体系结构
  * 可视化建模
  * 验证软件质量
  * 控制软甲变更
* ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114160706.png)

#### 3. 敏捷开发(适合需求模糊且经常改变的场合，适合商业竞争环境下的项目)，重点。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114165948.png)

* 为了解决瀑布流开发模式因为前阶段修改而导致的耦合问题
* 把一个大项目拆分为多个互相联系且能独立运行的小项目，分别完成，主体软件要随时能交互给用户
* 人员架构：
  * PO(Product Owner)：项目经理，确定产品方向愿景，交付优先级、时间
  * SM(Scrum Master)：小组队长
  * Team(DEV开发人员&QA测试人员)：

* 会议：
  * 敏捷计划会：一个Sprint开一次（一个sprint就是一个迭代），每一个Sprint的任务明确，需求分析，故事点划分
  * 每日立会：
    * 从昨天到现在，我完成了什么
    * 从现在到明天，我计划完成什么
    * 有什么阻碍我的发展，抛出困难和风险
  * 敏捷评审会：获取客户反馈
  * 敏捷回顾会：每个Sprint末尾开一次，总结经验和教训
    * 迭代速率，迭代燃气燃尽图
    * 迭代计划故事和实际完成故事
    * 计划发布日期和实际发布日期
    * 客户满意度，团队满意度，生产环境Bug数，生产Bug解决时间，用户故事
* 总的项目需要拆分，包括代码库页拆分，便于管理。立会时领取故事点完成，自行验证（静态检查，复杂度，测试用例，功能测试），git push后触发CI(持续集成)流程。有团队骨干代码评审（code review），如果都通过了就入库了。
* 主分支的代码随时可交付。
* 每个开发人员的开发互不影响。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114170108.png)

### 选择软件过程模型

* 软件过程模型是不断发展的
* 各种软件过程模型各有优缺点和使用场合
* 不同类型软件往往需要不同软件过程模型
* 选用时不必拘泥于某种模型
* 可组合多种模型
* 可根据实际创造新的模型

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201114170932.png)


