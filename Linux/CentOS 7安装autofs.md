# CentOS 7安装autofs

​		无论是 Samba 服务还是 NFS 服务，都要把挂载信息写入到/etc/fstab 中，这样远程共享资
源就会自动随服务器开机而进行挂载。虽然这很方便，但是如果挂载的远程资源太多，则会
给网络带宽和服务器的硬件资源带来很大负载。如果在资源挂载后长期不使用，也会造成服
务器硬件资源的浪费。  

​		autofs 自动挂载服务可以帮我们解决这一问题。与 mount 命令不同， autofs 服务程序是一
种 Linux 系统守护进程，当检测到用户视图访问一个尚未挂载的文件系统时，将自动挂载该
文件系统。  

## 安装

```
# yum install autofs
```

​		处于生产环境中的 Linux 服务器，一般会同时管理许多设备的挂载操作。如果把这些设
备挂载信息都写入到 autofs 服务的主配置文件中，无疑会让主配置文件臃肿不堪，不利于服
务执行效率，也不利于日后修改里面的配置内容，因此在 autofs 服务程序的主配置文件中需
要按照“挂载目录 子配置文件”的格式进行填写。  

​		挂载目录是设备挂载位置的上一级目录。例如，光盘设备一般挂载到/media/cdrom 目录中，那么挂载目录写成/media 即可。对应的子配置文件则是对这个挂载目录内的挂载设备信息作进一步的说明。子配置文件需要用户自行定义，文件名字没有严格要求，但后缀必须以.misc 结束。  

```shell
# vim /etc/auto.master

#
# Sample auto.master file
# This is a 'master' automounter map and it has the following format:
# mount-point [map-type[,format]:]map [options]
# For details of the format look at auto.master(5).

/home/confluence   /etc/back.misc
/misc   /etc/auto.misc
#
# NOTE: mounts done from a hosts map will be mounted with the
#       "nosuid" and "nodev" options unless the "suid" and "dev"
#       options are explicitly given.
#
/net    -hosts
#
# Include /etc/auto.master.d/*.autofs
# The included files must conform to the format of this file.
#
+dir:/etc/auto.master.d
#
# Include central master map if it can be found using
# nsswitch sources.
#
# Note that if there are entries for /net or /misc (as
# above) in the included master map any keys that are the
# same will not be seen as the first read key seen takes
# precedence.
#
+auto.master
```

 （主配置文件添加挂载目录的上一级目录 （空格）子配置文件目录） 

```
vim /etc/back.misc

bak_share    -fstype=nfs,rw,sync,root_squash 172.16.20.219:/home/nfs/confluence

```

 （子配置文件添加  挂载目录（空格）-文件系统类型，权限信息（空格）:被挂载目录） 

