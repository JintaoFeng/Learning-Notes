# 安装ntp

## server 安装

1. 确认是否安装ntp安装包  

```c
rpm -qa |grep ntp
```

![1645168174028](..\picture\1645168174028.png)

如果只有ntpdate，没有ntp,则删除ntpdate。

2. 安装

安装ntp-server需要有三个安装包，`autogen-libopts、ntpdate、ntp`。按照循序依次安装。

```
rpm -ivh autogen-libopts-5.18-5.el7.x86_64.rpm
rpm -ivh ntpdate-4.2.6p5-29.el7.centos.2.x86_64.rpm
rpm -ivh ntp-4.2.6p5-29.el7.centos.2.x86_64.rpm
```

3. 确认安装好后需要修改配置文件。

```c
vi /etc/ntp.conf
```

原始内容如下：

```
# Hosts on local network are less restricted.
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

```

修改为：

```
# Hosts on local network are less restricted.
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

#restrict 192.168.48.0 mask 255.255.255.0 nomodify notrap
#restrict 192.168.43.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst     #注释原本同步网址syste
server 127.127.1.0 iburst  #设置本机为ntp-server

```

4. 打开防火墙端口:

ntp服务使用的端口是123，用的是udp协议。

```
firewall-cmd --permanent --add-port=123/udp
serv
```

查看防火墙已打开端口。

```
[root@localhost ~]# iptables -L -n |grep 123
ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:123 ctstate NEW,UNTRACKED

```

5. 查看是否同步。

```
[root@localhost ~]# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*LOCAL(0)        .LOCL.           5 l   55   64  377    0.000    0.000   0.000

```

6. 设置开机启动。

```
systemctl enable ntpd
```





## client安装

安装步骤和服务器一样，对配置文件的修改有所区别。

```
vi /etc/ntp.conf
```

```
# Hosts on local network are less restricted.
restrict 172.16.20.216 mask 255.255.255.0 nomodify notrap
# 允许216的服务器修改
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst     #注释原本同步网址syste
server 172.16.20.216     #ntp-server IP
```

与本地ntp server同步一下

```shell
[root@localhost Downloads]# ntpdate -u 172.16.20.216
21 Feb 17:22:40 ntpdate[20562]: adjust time server 172.16.20.216 offset -0.000021 sec
```

查看ntp状态

```
[root@localhost Downloads]# ntpstat
synchronised to NTP server (172.16.20.216) at stratum 7
   time correct to within 46 ms
   polling server every 256 s
```

其余操作与server一致。