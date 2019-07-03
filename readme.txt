**原文地址：https://www.cnblogs.com/tocy/p/git-command-line-manual.html**

##0. 安装Git
网上有很多Git安装教程，如果需要图形界面，windows下建议使用TortoiseGit，linux建议使用Git GUI或者GITK。

##1. Git基本配置
git config可以配置git的参数，可以使用git config --list查看已经配置的git参数。其中有三个级别的保存位置，--system、--global、--local，分别表示所有用户（本系统）、当前用户（全局）、本地配置（当前目录），默认使用--local。

配置用户名及邮箱
在使用Git提交前，必须配置用户名和邮箱，这些信息会永久保存到历史记录中。
```git
git config --global user.name "Tocy"
git config --global user.email zyvj@qq.com
```

其他配置
如果在windows下建议还是配置下默认文本编辑器core.editor和差异分析工具merge.tool。

##2. 创建Git仓库
可以直接调用git init初始化当前目录，即创建Git仓库。

##3. 获得Git仓库
如果需要克隆远程仓库，可以使用git clone，比如：
```git
git clone https://git.oschina.net/Tocy/SampleCode.git
```
##4. 提交更新
在windows下的Git GUI中，提交很简单，右键-TortoiseGit-Commit。那么命令行下需要怎么处理？
Git中每个文件都有三种状态：committed、staged、modified。它们之间关系如下：
```git
commit <-- stage <-- modify
commit --> --- --modify
```

我们获取的Git仓库中的所有文件都是committed状态，如果你在本地修改了文件a，a的状态就变成modified的；如果使用git add a，a的状态变成staged；如果使用git commit，a的状态就变成commited。这种状态变化也说明复制代码是很方便的，但是提交更新请慎重。
当然还有一种文件状态，未跟踪状态（unversioned/untracked），通过使用git add可以把未跟踪状态变更为staged；通过git rm可以将staged或者committed状态变为未跟踪状态。
```git
git status
```
通常提交前先检查下修改了什么内容，当前Git目录下各文件的状态。
```git
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        Readme.md

nothing added to commit but untracked files present (use "git add" to track)
```
```git
git add
```
git add可以添加文件或者目录，也可以使用通配符。比如：
```git
git add Readme.md    # add file only
git add *.cpp        # add all cpp files
git add /home/code/  # add all files in /home/code
```
```git
git diff
```
git diff可以查看当前目录的所有修改。
提交之前，还是单独确认下处于staged状态的文件有哪些，并保证修改正确。在实际应用中，可能还需要使用git diff导出PATCH做代码走读。
可以使用git diff --staged或git diff --cached查看staged与上次提交快照之间的区别。
```git
git commit
```
提交前需慎重。直接调用git commit会弹出编辑器，输入提交日志（如果是多行日志，建议使用）。
针对单行日志提交的情况，可以使用如下命令：git commit -m "add readme"。
还有一种快捷的提交方式，直接跳过stage缓存区，直接提交当前目录下的所有修改git commit -a（使用这个命令前建议确认下当前目录的修改是否正确、必须）。
```git
git rm
```
git rm会把文件从当前目录删除（不会保存删除的文件）。如果需要从Git仓库中删除，但保留在当前工作目录中，亦即从跟踪清单中删除，可以使用git rm --cached readme.md。

##5. 提交历史查看
可以使用git log查看当前工程的所有提交的日志。
```git
git log --stat      # 仅显示摘要选项
git log --pretty=oneline        # 定制记录格式
git log --graph     # 图像化分支和版本更新
```

##6. 远程仓库
可以使用git remote查看当前的远程库。
```git
git remote -v
```
可以显示对应的克隆地址。（对于多个远程仓库很有用）

添加远程仓库
```git
git remote add [short_name] [url]  #可以添加新的远程仓库。
```
从远程仓库抓取数据
```git
git fetch [remote-name]   #可以从远程仓库抓取数据到本地。
```
也可以使用git pull

推送数据到远程仓库
```git
git push [remote_name] [branch_name]
```
默认使用origin和master。

查看远程仓库信息
```git
git remote show origin
```
远程仓库的删除和重命名
```git
git remote rename [old_name] [new_name]
git remote rm [remote_name]
```
##7. 打Tags
可使用git tag显示当前库中的标签。

添加标签（含附注）
```git
git tag -a v0.1 -m "my version 0.1"
```
使用如下命令查看Tag日志信息（指定对应标签的名字）
```git
git show v0.1
```
也可使用SHA-1的提交表示创建tag：
```git
git tag -a v0.2 [SHA-1] -m "my version 0.2"
```
分享标签
默认的，git push不会推送标签信息到远程仓库，需要通过命令显式推送。
```git
git push origin v0.1
```
如果需要推送所有标签，使用
```git
git push origin --tags
```
##8. Git分支
git分支是轻量级的，速度很快，仅记录索引信息。

显示所有分支
使用git branch可显示当前所有分支。
可以使用--merged和--no-merged查看已经合并、未合并的分支。

创建及切换分支
可以使用下面命令直接切换并创建分支
```git
git checkout -b testing
```
等价于
```git
$ git branch testing    # 创建testing 分支
$ git checkout testing  # 切换到testing分支
```
注意切换分支时请保持工作目录没有未提交的修改。Git鼓励使用分支，处理完问题之后合并分支即可。

分支合并
将hotfix分支合并到master（主分支）上，需要通过下面命令：
```git
$ git checkout master
$ git merge hotfix
```
合并之后可以使用git branch -d hotfix删除分支。
如果合并时存在冲突，需要手工修改。

