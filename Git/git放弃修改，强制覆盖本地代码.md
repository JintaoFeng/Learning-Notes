# git放弃修改，强制覆盖本地代码

在使用Git的过程中，有些时候我们只想要git服务器中的最新版本的项目，对于本地的项目中修改不做任何理会，就需要用到Git pull的强制覆盖，具体代码如下：

```git
git fetch --all
git reset --hard origin/master 
git pull
```

