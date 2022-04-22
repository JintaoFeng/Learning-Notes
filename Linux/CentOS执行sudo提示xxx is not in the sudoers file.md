# CentOS执行sudo提示xxx is not in the sudoers file

 在新安装的**CentOS**系统中,使用默认创建的用户执行`sudo`命令时终端报错： 

```
xxx is not in the sudoers file. This incident will be reported.
```

## 报错原因

**CentOS**默认创建的用户并没有`sudo`命令的执行权限，而且**CentOS**中也并不存在`sudo`用户组。

不同于**CentOS**，**Ubuntu**在安装后默认创建的用户属于`sudo`用户组，因此可以使用`sudo`命令。

## /etc/sudoers 文件简介

错误信息中提到的**sudoers file**位于`/etc/sudoers`，`root`用户使用`visudo`命令可对其进行查看，最开始的文件介绍内容如下：

概括来说，sudoers文件允许指定用户在运行命令时获取root权限而无需输入root密码。

根据最后一行，必须通过visudo命令来对/etc/sudoers文件进行编辑，该命令需要root权限。

sudoers文件可对多种类型的命令权限及用户组权限进行授权，预设的命令包括网络管理、软件安装与管理、服务管理、数据库升级、存储管理、权限代理、进程管理、驱动管理等，预设的命令如下：


## 解决办法

首先使用`root`用户运行`visudo`命令，打开`/etc/sudoers`文件，找到如下所示的片段：

```
## Next comes the main part: which users can run what software on
## which machines (the sudoers file can be shared between multiple systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL

```

```
## Allow people in group wheel to run all commands
# %wheel ALL=(ALL)       ALL

## Same thing without a password
# %wheel ALL=(ALL)       NOPASSWD: ALL

```

可知有两种方法可让指定用户获取`sudo`权限。

### 直接给指定用户授权

查阅网上相关博客，大多是基于此方法。例如用户名为`test`，直接给`test`用户授权，只需在`root`用户的授权定义下添加相同的授权定义，将用户名改为`test`：

```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
## Allow test to run any commands anywhere
test    ALL=(ALL)       ALL

```

这种方法虽然简单却也比较极端：例如将test用户删除后忘记删除sudoers中的授权，之后再次新建同名账户的话，test用户就直接获得了sudo权限，存在安全隐患；而且每次新建用户后都需要再次添加授权定义，操作很麻烦，因此推荐使用下面的方法。

将指定用户加入wheel用户组
可以注意到，在sudoers文件中可对wheel用户组整体授权，因此可先将用户test加入用户组wheel中：


```
$ su - root
$ usermod -G wheel test
```

 之后通过`visudo`命令在`sudoers`文件中对`wheel`用户组进行授权，分为**需要密码**和**无需密码**两种方式，取消掉任意一种授权前面的注释即可： 

```
## Allow people in group wheel to run all commands
%wheel ALL=(ALL)       ALL

## Same thing without a password
# %wheel ALL=(ALL)       NOPASSWD: ALL

```

