## 分支管理

### 创建`dev`分支，然后切换到`dev`分支

```
git checkout -b <name>
git switch -c <name>
```

`git checkout`命令加上`-b`参数表示创建并切换，`git switch`命令加上`-c`参数也是，相当于以下两条命令：

```
git branch <name>
git checkout <name>
```

### 查看当前分支

```
git branch
```

`git branch`命令灰列出当前所有分支，当前分支前面会标出一个`*`号。

```
git branch -r
```

查看远程分支列表

```
git branch -a
```

查看所有分支列表，包括远程和本地。

```
git branch -vv
```

查看本地分支对应的远程分支。

```
git branch -m oldname newname
```

给分支重命名。

### 切换分支

```
git checkout <name>
git switch <name>
```

###  合并分支

```
git merge <name>
```

`git merge`命令用于合并指定分支到当前分支。

### 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

 用`git log --graph`命令可以看到分支合并图。 

### git无法显示中文文件名解决

```
git config --global core.quotepath false
```

### 删除分支

```
git branch -d <name>
```

### 分支管理

通常，合并分支时，git会用`Fast forward`模式，在这种模式下，删除分支会丢掉分支信息，我们可以加上`--no-ff`参数禁用`Fast forward`模式，git在merge时生成一个新的commit。

```
$ git merge dev
Updating 6992160..c7076aa
Fast-forward
 Git/git总结.md | 53 +++++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 53 insertions(+)
 create mode 100644 Git/git总结.md
```

```
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 Git/git总结.md | 24 ++++++++++++++++++++++++
 1 file changed, 24 insertions(+)
```

```
$ git log --graph --pretty=oneline --abbrev-commit
*   1181ecd (HEAD -> master) merge with no-ff
|\
| * eda9603 (dev) 添加总结文档部分内容
|/
* c7076aa 修改git总结.md文件
* 6992160 (origin/master, origin/HEAD) 修改机械臂动力学文件
* f850214 添加轨迹规划
* 39ffa90 修改markdown的图片路径
*   e73aaa4 Merge branch 'dev'
|\
| * 9789173 (origin/dev) 添加Git/git-cheatsheet.pdf文件
* | 1fc1179 添加精密控制的几篇论文
* | d27285d 机械臂动力学笔记
|/
* f7affb2 添加机械臂动力学文件
* 4502d72 第一次提交


```

可以看到不加参数和加上参数的分支历史不太一样。

在软件开发中，我们经常需要在本分支的工作还没完成的情况下去处理另一个分支的bug，但是我们在本分支的工作还没完成，无法提交，这时我们可以先将工作现场储存起来，等到另一分支的工作完成后再回到本分支处理恢复现场。`git stash`命令就是储存当前现场。

```
$ git stash
Saved working directory and index state WIP on master: 1181ecd merge with no-ff
```

然后我们就可以按到现在的工作空间是干净的。

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

可以使用`git stash list`命令查看储存的工作现场

```
$ git stash list
stash@{0}: WIP on master: 1181ecd merge with no-ff
```

恢复现场可以用两个命令。一是`git stash apply`,使用这个命令后，stash的内容并不删除，需要用`git stash drop`来删除。另一种方式是用`git stash pop`,恢复的同时删除stash内容。

```
$ git stash pop
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Git/git总结.md

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (d867385628343cc38f8e31c6944f20accb367e15)

```

我们在其他分支修复的bug可以通过`git cherry-pick <commit>`命令来将提交的修改复制到当前分支。<commit>是你在其他分支提交的编号。

当新分支还没有合并的时候，我们需要删除分支，这个时候Git会报错，因为还没有合并，所以不能删除，这时我们可以强制删除，使用`-D`参数。

```
git branch -D <name>
```

在本地创建远程的其他分支

```
git checout -b dev origin/dev
```

当`git pull`提示` no tracking information `，说明本地分支和远程分支没有建立链接关系，可以用以下命令创建。

```
git branch --set-upstream-to <branch-name> origin/<branch-name>
```

## 版本管理

### 版本回退

在工作过程中，我们有时会需要把版本进行回退，我们可以用`git log`来查看相对应的版本，然后用`git reset --hard HEAD^`回退到上一个版本号。上上一个版本就是`HEAD^^`。向后N个版本就是`HEAD~N`。

用`git reflog`可以查看所有的命令，就可以查看以前每一步操作的commit id。然后`git reset --hard <commit id>`

### 撤销修改

```
git checkout --file
```

这个命令可以让file回到最近一次`git commit`或`git add`时的状态。

### 删除文件

```
git rm <file>
```

先手动删除文件，然后`git add <file>`和直接使用`git rm <file>`效果是一样的。

## 标签管理

在发布新版本时，我们可以给版本库打上一个标签，我们可以通过标签将某一个版本取出来。跟`commit`很像。

### 创建标签

```
git tag <name>
```

先 切换到需要打标签的分支上，敲命令即可。

可以用`git tag`查看所有标签。

也可以给某一个`commit`打上标签

```
git tag <name> <commit id>
```

可以通过`git show <tagname>`查看标签信息。

我们也可以给标签加上说明，用`-a`指定标签名，`-m`指定说明文字

```
git tag -a <tagname> -m <message> <commit_id>
```

* 标签总是和某个commit挂钩，如果这个commit寄出现在master分支，也出现在dev分支，那么在两个分支都可以看到这个标签。

### 操作标签

如果标签打错了，我们可以删除：

```
git tag -d <tagname>
```

创建的标签都存储在本地，不会推送到远程，所以打错的标签可以在本地安全删除。如果要推送标签到远程，可以用命令

```
git push origin <tagname>
```

或者一次性推送全部未到远程的本地标签

```
git push origin --tags
```

要删除远程标签，先要从本地删除，然后在删除远程：

```
git tag -d <tagname>
git push origin :refs/tags/<tagname>
```

## 忽略文件

在版本控制中，有一些编译生成的中间文件我们不想加入版本库中，但是不加入的话每次用`git status`命令又都会有提示，让人很烦，`.gitignore`文件可以帮我们忽略掉这些。`.gitignore`文件github已经为我们准备了各种配置文件，只需要组合一下就可以使用。[https://github.com/github/gitignore](https://github.com/github/gitignore)。最后记得把忽略文件也提交到版本库中。

如果我们需要新建一个`.gitignore`文件，因为Windows系统不支持无文件名的创建，所以可以从git中直接创建。

```
vim .gitignore
```

也可以新建一个文件，然后另保存，名字改为`.gitignore`.

有时候有的文件类型被忽略掉了，但是想添加其中一个文件，我们可以加`-f`参数，强制添加到版本库中

```
git add -f <filename>
```

要是`.gitignore`.文件写的有问题，可以用`git check-ignore`来检查

```
git check-ignore -v <filename>
```



## 配置别名

我们可以为git命令添加别名，让我们更好记忆。比如：

```
git config --global alias.st status
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

## 创建版本库

新安装的git我们先需要配置一下账户，

```
git comfig --alobal user.name "your name"
git config --global user.email "email"
```

然后用`git init`初始化文件为可管理的仓库。

```
git commit -m "message"
```

`-m`参数可以让我们对本次提交添加一些描述性语言，如果不加参数，是会用一个像是vim编辑器让你输入这个参数。

