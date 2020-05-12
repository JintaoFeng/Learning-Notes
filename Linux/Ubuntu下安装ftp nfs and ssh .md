## 安装FTP

打开Ubuntu的终端窗口，执行如下命令：

```
sudo apt-get install vsftpd
```

等待软件安装完成，然后执行如下命令

```
sudo vim /etc/vsftpd.conf
```

打开以后找到如下两行：

```
local_enable=YES
write_enable=YES
```

确保上面两行没有“#”.如下：

![](..\picture\微信截图_20200507194825.png)

修改完文件以后保存，然后执行如下命令重启ftp。

```
sudo /etc/init.d/vsftpd restart
```

## 安装NFS

后面进行Linux驱动开发的时候需要用到NFS网络服务，因此需要安装并开启Ubuntu下的NFS服务，可使用如下命令：

```
sudo apt-get install nfs-kernel-server portmap
```

我们可以先在Ubuntu下创建一个nfs文件夹来供nfs服务器使用，以后我们可以在开发板上通过网络文件系统来访问nfs文件夹，使用如下命令来打开nfs配置文件。

```
sudo vim /etc/exports
```

打开配置文件后在下面添加如下内容：

```
/home/fengjt/linux/embedded/nfs *(rw,sync,no_root_squash)
```

![](..\picture\微信截图_20200507212713.png)

添加完了如上图所示，然后保存退出，重启NFS服务。

```
sudo /etc/init.d/nfs-kernel-server restart
```

![](..\picture\微信截图_20200507212938.png)



## 安装SSH

开启Ubuntu的SSH服务以后我们就可以在Windows下使用终端软件登录到Ubuntu。Ubuntu下使用如下命令开启SSH服务。

```
sudo apt-get install openssh-server
```

ssh的配置文件为/etc/ssh/sshd_config