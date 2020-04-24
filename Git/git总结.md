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

