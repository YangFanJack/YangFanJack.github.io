# git基础


## 	git的安装

​		[官网下载](https://git-scm.com/)后，在cmd中输入git验证是否安装完成

## 	git的基本配置

~~~ 
git config --global user.name "name"
git config --global user.email "email"
~~~

​		**需要注意的是可以有三种参数，local、global和system，优先级依次降低。global是全局配置，local可以为本地项目添加单独配置**

​		**查看配置命令：**

~~~
git config -l --global(或其他两个)
~~~



> git配置文件位置

1. 本地项目配置文件：`当前项目/.git/config`

   ```
   git config --local XXX
   ```

2. 全局配置文件：`~/.gitconfig`

   ```
   git config --global XXX
   ```

3. 系统配置文件：`/etc/.gitconfig`（需要root权限）

   ```
   git config --system XXX
   ```



> 三种免密登录方式

1. URL中体现：

   ```
   原来：https://github.com/YangFanJack/DesignPattern.git
   免密：https://用户名:密码@github.com/YangFanJack/DesignPattern.git
   
   git remote add origin https://用户名:密码@github.com/YangFanJack/DesignPattern.git
   git push origin master
   ```

2. SSH实现：

   ```
   //生成公钥和私钥，在用户的.ssh文件夹里面把公钥添加到远程仓库github里面
   ssh-keygen -t rsa -C "youremail"      //生成两把钥匙
   ssh -T git@github.com  //验证ssh配置是否配置好
   
   //注意：如果提示找不到ssh-keygen命令的错误，说明你执行这条命令的位置错了，应该在本地的git仓库根目录下执行
   ```

3. git自动管理凭证

   ```
   //基于操作系统的功能，不同操作系统的实现方式不一样
   ```

   

## 	git基本操作

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521204745.png)

​		**第一步**

~~~
git init //初始化，让git帮助管理文件夹
git status //检测当前目录下文件状态
git add //工作区到暂存区
git commit -m "注释" //暂存区到版本库，生成版本/快照
git diff //查看修改内容
# git status显示不了中文解决方法
git config --global core.quotepath false
# 解决git-bash终端乱码问题
git-bash的设置-文本-locale-zh_CN-utf-8
# 启动bash.exe的命令加上参数-i -l
~~~

​		**从现在回溯到历史，和从历史回溯到将来**

~~~
git log //查看提交历史
git log --pretty=oneline --graph --abbrev-commit //以图线的方式精简显示版本历史

//git中使用HEAD表示当前版本
git reset --hard 版本号(HEAD^,HEAD^^,HEAD~n) //回滚

//如果想从前面回到后面，使用git reflog查看你的每一次操作，然后使用git reset --hard
git reflog //查看命令历史
git reset --hard 版本号
~~~

​		**git reset 三种模式**

```
git reset --hard 版本号：重置stage区和工作目录

git reset --soft 版本号：保留工作目录，并把重置 HEAD 所带来的新的差异放进暂存区

git reset --mixed(默认) 版本号：保留工作目录，并清空暂存区

# 虽然在你的本地分支中使用 git reset 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的
为了撤销更改并分享给别人，我们需要使用 git revert，revert是一个commit一个commit地提交
git revert commitID
# 比较常见的一种场景是：merge了一个分支后需要恢复到merge前的状态，我们可以git revert 新生成的merge commitID，不通的一点是父ID不止一个，需要指定。1和2根据merge的log里的commitID的先后顺序
git revert commitID -m 1/2
```

​		**与版本库内容进行比较**

```
//显示暂存区和工作区的差异
git diff 文件名
//显示暂存区和上一次提交(commit)的差异
git diff --cached 文件名
git diff --staged 文件名
```

​		**暂存区文件撤销**

```
git reset HEAD 文件名
git rm --cached 文件名
git restore --staged 文件名

//如果暂存区有该文件，工作区和暂存区统一，如果没有，工作区和版本库统一
git checkout -- 文件名
```

​		**git的分支操作**

~~~
git branch //显示本地所有分支
git branch -r //显示远程所有分支
git branch -a //显示本地和远程所有分支
git branch 分支名 //创建分支
git checkout 分支名 //切换分支
git checkout -b 分支名 //创建并切换分支
git branch -d 分支名 //删除分支
git merge 其他分支名 //合并其他分支到当前分支，可能产生冲突，手动修改冲突文件
git branch -f main HEAD~3：将 main 分支强制指向 HEAD 的第 3 级父提交，git checkout只能移动HEAD到某个分支的最后一个commit，但是git branch -f能自定义某个分支的最后一个commit到任意节点
~~~

​		**git的删除操作**

~~~
//手动删除工作区的文件后可以恢复
//git restore指令使得在工作空间但是不在暂存区的文件撤销更改(内容恢复到没修改之前的状态)
git restore 文件名

git rm (-r -f) XX
git commit -m "直接同步删除操作到本地仓库"

rm (-r -f) XX
git add XX
git commit -m "需要先add，再同步删除操作到本地仓库"
~~~

> **问题：**
> 远程已经修改，然后你没pull就修改了本地，push时发生了错误
> **解决：**
> 错误发生后，先pull，在本地中的冲突文件中按照需求修改文件，最后再push



**经过上述步骤已经能实现本地多分支开发，假设目前开发进度如下图所示：**

![gnome-shell-screenshot-9CZIY0.png](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgusachwRCOLndJli.png)

​		**远程仓库操作（以github为例）**

```
//查看关联的远程仓库
git remote
git remote -v
git remote show origin

//连接可以是http，也可以是ssh
git remote add origin http://github.com/YangFanJack/xxxxx.git
git remote rm origin
//-u表示以后默认都是origin这个地址
git push -u origin master
//刷新远程跟踪分支
git remote update origin --prune

//git clone已经包含了remote add origin操作
git clone http://github.com/YangFanJack/xxxxx.git

//接上图继续开发
git checkout dev //切换到需要拉取并合并的分支
git merge master //将dev更新为最新版本[仅1次]（上次开发结束时最新版本在master主分支上）
（继续开发...）
git add .
git commit -m XXX
git push origin dev

//异地拉取代码继续开发
git checkout dev
git pull origin dev
（继续开发...）
git add .
git commit -m XXX
git push origin dev

//某一个阶段代码开发完后，合并dev到master
git checkout master
git merge dev
git push origin master

git checkout dev
git merge master
git push origin dev
```

​		**pull=fetch+merge**

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521204911.png)

```
git pull origin dev

git fetch origin dev
git merge origin/dev
```



## rebase(变基)

​		**应用场景一：多版本记录合并为一个版本记录**

```
git rebase -i HEAD~2 //HEAD和之前的1个合并
git rebase -i 版本号 //HEAD到该版本之前合并

//注意：合并版本的时候不要包含已经push到远程仓库的版本
```

​		**应用场景二：将另一个分支(dev)的版本强插到当前版本(master)中**

```
git checkout dev
git rebase master
git checkout master
git merge dev
```

​		**应用场景三：pull后产生冲突（公司上次开发没提交，在家继续开发，去公司pull代码时）**

```
//会产生分支
git pull origin dev = 
git fetch origin dev + git merge origin/dev

//不会产生分支
git pull origin dev --rebase = 
git fetch origin dev + git rebase origin/dev

//git fetch这个命令会把远程的commits拉取到本地的repo中，但是，它不是直接把commits接在分支的最后面，而是从你最后一次push的那个commit节点，再拉取一个新的分支出来，类似这样：
   * git fetch拉下来的节点，建立在一个新的分支上
* /  你还没push的节点
|/
* 你最后一次push的节点
|
*
```

**注意：git rebase可能有冲突，解决后 git add，然后 git rebase --continue 就可以**



## beyond compare冲突解决软件

1. 安装bcompare软件

2. 在git中配置

   ```
   git config --local merge.tool bc3
   git config --local mergetool.path 'usr/bin/bcompare'
   git config --local mergetool.keepBackup false
   ```

3. 调用beyond compare解决冲突

   ```
   git mergetool
   ```



## 多人协同开发

#### gitflow工作流

![gnome-shell-screenshot-0W3HY0.png](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgkqFIWSKabYZci9m.png)

​		**协同开发的两种方式：**

1. 自己账户下创建仓库，然后在setting里邀请其他github用户参与项目
2. 建立组织，在组织下创建仓库，再邀请其他github用户参与项目



​		**建立tag标签：**

```
git tag -a v1 -m "第一版" //默认是HEAD处创建tag
git tag -a v1.2 9fceb02 -m "my tag"
git tag -d v1 //删除版本

git tag //列出本地所有tag

git push origin --tags

git show v1.0 //显示标签处的commit

git tag -d <tagname> //删除一个本地标签；
git push origin :refs/tags/<tagname> //删除一个远程标签。
```



#### 协同开发第一步：master分支建立

```
git init
git touch TestClass.java
vim TestClass.java
javac TestClass.java
java TestClass

git add .
git commit -m "Hello,world!"
git remote add origin git@github.com:queer-coder/test.git
git push -u origin master

git tag -a v1 -m "第一版"
git tag

git push origin v1.0
git push origin --tags
```

#### 协同开发第二步：dev分支，ddz分支建立

```
git branch
git checkout -b dev

//邀请另外用户进组织
//组织中setting可设置成员权限，默认是只读
//项目中setting可以单独设置成员拥有写权限

//另一个用户clone代码到本地开发斗地主分支
git checkout -b ddz
touch Doudizhu.java
vim Doudizhu.java
javac Doudizhu.java
java Doudizhu
git add .
git commit -m "斗地主"
git push origin ddz

vim Doudizhu.java
javac Doudizhu.java
java Doudizhu
git add .
git commit -m "斗地主开发完毕"
git push origin ddz
```

#### 协同开发第三步：dev管理者review ddz开发者代码

> 注意：
>
> release——》master分支具体是需要线上代码review还是直接本地merge要看项目需求
>
> 开发分支——》dev分支必须要线上代码review，不能本地merge

**通过github的pull request来实现代码review**

​	**dev成员(owner)：**打开项目的setting——》branches——》add rule（跟dev合并必须要经过review）——》(开发成员pull request后)在pull request中add your review，然后merge pull request就实现了review后的同意合并，最后可删除ddz开发分支

​	**斗地主开发成员(member)：**new pull request——》compare to base

```
//在线上github中同意merge后，线上dev已经最新了
//dev管理者owner本地再pull一下，本地就也就最新了
git pull origin dev
```

#### 协同开发第四步：测试

```
//代码测试分支由owner创建push到github
git checkout dev
git checkout -b release
git push origin release

//代码测试人员从github拉取release分支来进行代码测试

//测试完后，将release版本merge到master分支和dev分支（同样采用review的方式来pull request）
//删除release分支

//本地的master拉取线上master，打上第二版标签
git pull origin master
git tag -a v2 -m "第二版"
```



## 如何给开源项目贡献代码

> fork + pull request

1. fork源代码
2. git clone 自己仓库的源代码到本地
3. 做二次开发，bug修复
4. 给开源项目作者提交 修复bug 的申请（pull request），把自己仓库的某个分支提交给原作者
5. 等待源代码作者审核通过



> 实际开发中一些约定的分支名：

- master: 主分支，主要用来版本发布。
- develop：日常开发分支，该分支正常保存了开发的最新代码。
- feature：具体的功能开发分支，只与 develop 分支交互。
- release：release 分支可以认为是 master 分支的未测试版。比如说某一期的功能全部开发完成，那么就将 develop 分支合并到 release 分支，测试没有问题并且到了发布日期就合并到 master 分支，进行发布。
- hotfix：线上 bug 修复分支

![image-20210323154250824](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210323154250824.png)

## 再探：

1. 千万不要使用Windows自带的**记事本**编辑任何文本文件。原因是Microsoft开发记事本的团队使用了一个非常弱智的行为来保存UTF-8编码的文件，他们自作聪明地在每个文件开头添加了0xefbbbf（十六进制）的字符，你会遇到很多不可思议的问题，比如，网页第一行可能会显示一个“?”，明明正确的程序一编译就报语法错误，等等，都是由记事本的弱智行为带来的。建议你下载Visual Studio Code代替记事本，不但功能强大，而且免费！

2. 提示：changes not staged for commit

   解决：应该先git add

3. 提示：changes to be commit

   解决：表示可以git commit了

4. 提示：nothing to commit，working tree clean

   解决：没有需要提交的修改，且工作目录是干净的

5. git diff 比较的是工作区文件与暂存区文件（上次git add 的内容）的区别

   * 当暂存区中没有文件时，git diff比较的是，工作区中的文件与上次提交到版本库中的文件。
   * 当暂存区中有文件时，git diff则比较的是，当前工作区中的文件与暂存区中的文件。

   git diff --cached 比较的是暂存区的文件与仓库分支里（上次git commit 后的内容）的区别

   git diff HEAD 比较的是工作区中的文件与版本库中文件的差异

6. 通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

   如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

   合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

   ```bash
   #因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去
   git merge --no-ff -m "merge with no-ff" dev
   ```

   ![git-br-policy](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img0)

7. 你正在开发一个新功能，突然接到一个bug，必须优先解决bug，此时可以使用stash把当前工作区现场“储藏”起来，然后新建分支改bug再合并后，恢复之前”储藏“的工作现场就行了

   ```bash
   git stash //储藏工作现场
   ... //改bug
   git stash list //查看工作现场列表
   //有两种方法恢复
   //1. 一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除
   git stash apply
   git stash drop
   //2. 另一种方式是用git stash pop，恢复的同时把stash内容也删了
   git stash pop
   ```

8. 为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支

9. 多人协作开发：

   ```bash
   git clone git@github.com:michaelliao/learngit.git
   git checkout -b dev origin/dev
   git add env.txt
   git commit -m "add env"
   git push origin dev
   #push发现有冲突
   git pull
   #pull也失败，因为没有建立远程和本地的关闭
   git branch --set-upstream-to=origin/dev dev
   git pull
   #解决冲突后最后push成功
   ```

10. 标签是指向commit的死指针，分支是指向commit的活指针

11. 自定义Git

    * 让Git显示颜色：git config --global color.ui true

    * 忽略特殊文件：.gitignore文件

      * 忽略原则：
        1. 忽略操作系统自动生成的文件，比如缩略图等；
        2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
        3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

      ```
      # Windows:
      Thumbs.db
      ehthumbs.db
      Desktop.ini
      
      # Python:
      *.py[cod]
      *.so
      *.egg
      *.egg-info
      dist
      build
      
      # My configurations:
      db.ini
      deploy_key_rsa
      ```

      * 检验`.gitignore`的标准是`git status`命令是不是说`working directory clean`

      * 使用命令`git check-ignore -v App.class`检查文件是否被忽略

      * 把指定文件排除在`.gitignore`规则外的写法就是`!`+文件名，所以，只需把例外文件添加进去即可。

        ```
        # 排除所有.开头的隐藏文件:
        .*
        # 排除所有.class文件:
        *.class
        
        # 不排除.gitignore和App.class:
        !.gitignore
        !App.class
        ```

    * 配置别名

      git config --global alias.st status

      ```
      git config --global alias.st status
      git config --global alias.co checkout
      git config --global alias.ci commit
      git config --global alias.br branch
      ```

12. rebase

    ![img](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521205143.png)

    ![img](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521205203.png)

13. **致命问题：两个分支合并后为什么还存在不同？**

    答：首先需要明确的是：git的merge以来的不是不同分支文件内容的对比，而是不同分支的commit的对比

    举个例子：

    ```bash
    git merge test //在dev上合并test，此时dev包含test
    ... //然后在dev上新建一个分支hotfix，把刚刚从test合并来的内容中，删除一些新增文件，增加一些删除文件，增删改一些修改行，然后将hotfix合并到dev
    git merge test //再进行一次第一步操作，此时发现虽然merge了，但是dev并不包含test中修改的内容了，因为此时dev包含了test的所有commit，此足矣。
    ```

    **如何改进工作中对git使用来避免此类问题的发生？**

    工作中的产生原因：公司中项目分为test、dev、release、master分支，pull到本地新建xx-test、xx-dev分支进行修改，然后合并到对应的test、dev分支。经常还在线上的test、dev、release之间相互merge，导致问题出现的原因就是这个”相互“。

    要避免这样的问题就要强制要求提交顺序，应该按照功能新建分支来完成
14. 只需要部分commit中的内容？
   git rebase -i xx
   git cherry-pick xx

15. Git的一些奇技淫巧
* git stash save "XXX"
* git log --oneline --graph
* git push :branch_name
* git rm --cached filename
* git commit --amend
* git cherry-pick commit-id
* git archive-o archive.zip master

16. 压缩提交记录的三种方法：
* git reset commit-id
* git commit --amend "xxx"
* git rebase -i commit-id
