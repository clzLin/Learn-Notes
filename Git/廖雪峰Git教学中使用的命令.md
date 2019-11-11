以下命令都是在Git Bash下的

## 1. 安装完成后,设置用户名和邮箱 ##
```
git config --global user.name "Your Name"
git config --global user.eamil "email@example.com"
```
注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。


## 2. 将一个空目录初始化成Git仓库(版本库repository) ##
```
先进入该空目录,然后(注意该目录路径不要有中文)
git init
```

## 3.把文件添加到版本库 ##
```
git add readme.txt
git commit -m "wrote a readme file"
-m ""后面带的说明可以方便日后看本次提交修改了什么内容

可git add多个文件,然后一次性git commit
```

```
删除暂存区内的修改：
git rm --cached readme.txt
```

## 4.修改文件内容后，查看与版本库最新提交的该文件有哪里修改 ##
用于修改了代码后，出去了一趟，回来不知道改了哪里了，可以查看
```
git status//查看本地文件是否与版本库内不同，是否已提交的状态
git diff readme.txt//详细列出本地文件与版本库内具体哪几行不同
```

## 5.查看提交版本库的历史记录 ##
```
git log//按修改最新日期到最老日期排序
git log --pretty=oneline//简化输出信息，每条记录内容只显示一行
注意：前面显示的十六进制UUID是每条记录的ID号
```

## 6.版本回退 ##
```
git reset --hard HEAD^	//回退到上一版本
//几个^代表前几个版本;
//HEAD~100 回退100个版本
```
## 7.版本前进 ##
当版本回退后，又想回到最新的，
```
1.记得那个最新的版本提交记录的ID时，直接
git reset --hard xxxx

2.git reflog 
查看所有操作的记录，可以看到所有你提交以及别的操作的信息，包括ID
然后再用1即可
```

## 8.工作区 暂存区 Master分支概念 ##
因为有图例解析，更直观，网址：
https://www.liaoxuefeng.com/wiki/896043488029600/897271968352576

## 9.撤销工作区的修改 ##
在工作区内修改了文件内容后，想回退到未修改前的，和版本库内的暂存区一样，又记不起自己改了哪里了
```
命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修
改全部撤销，这里有两种情况：

一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本
库一模一样的状态；

一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添
加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。
```

## 10.撤销暂存区的修改 ##
如果在写了一些错误内容后，又不小心git add到版本库的暂存区了，那么需要撤销在暂存区的修改
```
git reset HEAD readme.txt
回退到和版本库中的Master分支一致
```

## 11.删除与恢复文件 ##
情况1. 当在工作区内的一个文件不再需要时，也想把版本库内的该文件删除
```
1. 先手动删除工作区内的该文件
2. git rm readme.txt //获取git add readme.txt
3. git commit -m "remove the readme.txt"
```

情况2. 工作区内的文件被误删，那么可以从版本库中恢复回来(前提是提交过)
```
git checkout -- readme.txt
```

发现又是git checkout,其实该命令就是把版本库内最新的该文件复制到工作区，所以不管是恢复修改还是恢复整个文件都是用该命令。

## 12.远程仓库之一Github ##
首先需要将本地仓库与Github关联起来
```
第一步：创建SSH Key
进入用户主目录：cd ~
ssh-keygen -t rsa -C "lin19768h@163.com"
然后一路回车，会生成id_rsa和id_rsa.pub两个文件

第二步：登陆GitHub,在SSH and GPG keys页面将id_rsa.pub内容复制到SSH Key
```

## 13.本地仓库关联远程仓库 ##
```
1. 先在Github创建一个新的空repository,叫learngit
2. 在Git bash下本地仓库目录下 使用命令：
   git remote add origin git@github.com:clzLin/learngit.git
添加后，远程库的名字就叫origin，也可以取别的，一般都用origin
```

## 14.将本地库的所有内容推送到远程库 ##
首次：
```
git push -u origin master
//实际上是把当前分支master推送到远程。
//首次要加-u，且第一次使用Git的clone或push命令后，会得到一个警告：
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
直接点yes，之后就不会有了
```

## 15.将远程库的内容克隆到本地仓库 ##
```
1. 先在Github创建一个新的repository叫gitskills，勾选自动生成readme.md
2. git clone git@github.com:clzLin/gitskills.git
```

## 16.分支的概念以及Git下分支的理解 ##
https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424

## 17.创建分支与切换分支与合并分支 ##
```
git checkout -b dev
//创建并切换到dev分支，相当于以下2条命令
git branch dev    //创建分支
git checkout dev  //切换分支
//刚创建的分支所在位置是和当前所在分支位置一样的

git branch
//列出所有分支：
* dev
  master
当前分支前会有个*号，且颜色为绿色
```

```
在dev分支上进行了一些修改后，版本库内关于该分支的进度就往后走了，master
分支还是在原来位置。
可以这样设计：
每天完成的工作我们先利用dev分支进行提交更新到版本库
每周后，将dev分支的内容合并到Master分支，命令如下：
1.git checkout master   //先切换回master分支
2.git merge dev         //把dev分支合并到master
```

## 18.删除分支 ##
```
git branch -d dev
注意：要先切换到非dev分支才能执行该命令来删除dev分支
``` 

## 19.操作分支的另一个命令:switch ##
```
因为前面讲的切换分支用“git checkout 分支名”，和最前面的撤销修改一样都用checkout
新版Git提供了新的git switch命令来切换分支

1. 创建并切换分支
   git switch -c dev
2. 直接切换到已有分支
   git switch master

switch单词更好体现切换的意思。
```

## 20.解决分支间的冲突 ##
https://www.liaoxuefeng.com/wiki/896043488029600/900004111093344

## 21.查看各分支的历史纪录(图形化) ##
```
git log --graph --pretty=oneline --abbrev-commit
```

## 22.BUG分支 ##
要临时解决一个Bug，但是当前分支内有文件正在处理，且还不能提交到版本库，因为切换分支后，当前分支下的工作空间的文件会被后一个分支的文件覆盖。
因为Bug都会创建一个临时分支来解决，所以需要将当前分支的文件先放到一个地方。
```
git stash      //将当前分支工作区的文件保存
git stash list //查看当前分支下保存过的记录
git stash apply//恢复保存内容，stash内容不删除
git stash pop  //恢复保存内容，stash内容删除
stash内容删除后，使用git stash list命令将看不到记录了
当保存了多次时，可以在git stash list记录下查看，找到需要恢复的目标
然后 git stash apply stash@{0}	//恢复第几个
```
处理Bug的步骤：
1. git stash 保存当前分支dev下的文件
2. git switch master 切换到master分支
3. git switch -c issue-01 在mster分支上创建新的子分支issue-01
   该子分支的内容与master分支一样，并切换到该issue-01子分支上
4. 在issue-01子分支上修复BUG，BUG修改好后，提交。
5. git switch master 切换回master分支
6. git merge --no-ff -m "merged bug fix issue-01" issue-01合并到master分支
7. git switch dev 切换回dev分支
8. git stash pop 恢复之前被BUG打断的正在进行的工作文件(git stash)
9. 既然有BUG，那么除了master上，其他各个由master生成的分支都有该BUG，dev也是，但是在每个分支上又重复一遍修改BUG，显然太繁琐
10. 复制修改BUG完后，那次修改提交到dev分支。这样BUG在dev分支也就解决了
首先要记住那次BUG提交时的ID号
然后在dev分支上使用 git cherry-pick id号，复制修改，即可修复bug。


## 23.Feature分支 ##
每个新增加的功能一般都要新建一个子分支来完成它，完成后，合并到工作主分支dev上，然后删除该子分支。
当使用该分支开发出该功能后，提交到版本库后，还未来的及合并到dev分支，这时候不需要该功能了，但是需要删除它，这时候使用
```
git branch -d feature 会报错
error: The branch 'feature' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature'.

需要使用-D参数来强制删除
```
## 24.团队协作 ##
复习前面学的本地版本库推送到远程库
```
git push origin master  //推送master分支到远程库
git push origin dev     //推送dev子分支到远程库
```
从远程库clone到本地库
```
git clone git@github.com:clzLin/learngit.git
但是该命令复制该版本库的所有分支到本地库，但是因为本地没有建立除了
master的其他分支，所以利用git branch只能看到*master。

小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：
git checkout -b dev origin/dev
```

团队协作避免不了会发生冲突，别人提交的和自己提交的存在冲突，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送
```
1.git branch --set-upstream-to=origin/dev dev //指定本地dev分支与远程origin/dev分支的链接
2.git pull //将当前所在分支关联的远程分支 拷贝到本地分支。
3.解决上一步执行后，会自动与你本地文件进行比较，若存在的冲突，解决完后提交，再推送到远程库。
```

因此，多人协作的工作模式通常是这样：

首先，可以试图用git push origin <branch-name>推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

## 25.标签(Tag) ##
当要发布一个新版本时，一般都会打上一个标签作为版本号，方便阅读与查找，标签就相当于Commit的一个快照。
```
git tag <name>
//给当前分支的最新一次Commit打上标签，名字为<name>
git tag 
//查看所有标签
git tag <name> commit_ID
//指定某次提交ID为commit_ID的Commit打上标签<name>
git show <name>
//查看指定标签的详细信息
git tag -a v0.1 -m "version 0.1 released" 1094adb
//给1094adb的Commit打上标签v0.1,标签的说明为"version 0.1 released"
git tag -d <name>
//删除标签
git push origin v1.0
//远程推送标签
git push origin --tags
//一次性推送全部尚未推送到远程的本地标签
1.git tag -d v0.9	//先删除本地标签
2.git push origin :refs/tags/v0.9
//远程删除标签
```

## 26.配置命令别名 ##
```
git config --global alias.st status
//这样，敲git st就等于git status
//带--global 说明是全局参数，所有Git仓库下都可以用该别名
//不带--global 只针对当前仓库下

//全局参数 在用户主目录下的一个隐藏	文件.gitconfig中
//没个仓库的配置文件在当前仓库目录下的.git/config文件中
```
```
git config --global alias.lg "log --color --graph --
pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%
cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

以后看提交记录就方便了。
```
