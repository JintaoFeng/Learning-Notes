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

通常，合并分支时，git会用`Fast forward`模式，在这种模式下，删除分支会丢掉分支信息，我们可以加上`--no--ff`参数禁用`Fast forward`模式，git在merge时生成一个新的commit。

```
$ git merge dev
Updating 6992160..c7076aa
Fast-forward
 Git/git总结.md | 53 +++++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 53 insertions(+)
 create mode 100644 Git/git总结.md

```

```

```

