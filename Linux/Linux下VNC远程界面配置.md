# Linux下VNC远程界面配置

## 简介

VNC（Virtual Network Computing）是一套实现远程网络操控的软件。
网络操控技术是指由一部计算机（主控端）去控制另一部计算机（被控端），而且当主控端在操作时，就如同亲自在被控端前操作一样，可以执行被控端的应用程序，及使用被控端的系统资源。

VNC软件主要由两个部分组成： VNC Server及VNC Viewer Client。

- 用户需先将VNC Server安装在被控端的计算机上，才能在主控端执行VNC Viewer Client控制被控端。
- VNC Server与VNC Viewer Client支持多种操作系统，如Unix系列、Windows及MacOS，因此可将VNC Server及VNC Viewer Client分别安装在不同的操作系统中进行控制。
- 如果目前操作的主控端计算机没有安装VNC Viewer Client，也可以通过一般的网页浏览器来访问，进而控制被控端。

整个VNC运行的工作流程

1. VNC客户端通过浏览器或VNC Viewer Client连接至VNC Server。
2. VNC Server传送一个对话窗口至客户端，要求输入连接密码，以及存取的VNC Server显示装置。
3. 在客户端输入联机密码后，VNC Server验证客户端是否具有存取权限。
4. 通过VNC Server的验证后，客户端将立即要求VNC Server显示桌面环境。
5. VNC Server将获得的桌面环境利用VNC通信协议送至客户端，并且允许客户端控制VNC Server的桌面环境及输入装置。

# Free VNC tools

TigerVNC

- Homepage：http://tigervnc.org/
- Download：https://bintray.com/tigervnc/stable/tigervnc/

TightVNC

- Homepage：http://www.tightvnc.com/
- Download：https://www.tightvnc.com/download.php

RealVNC的VNC Viewer Client

- https://www.realvnc.com/en/connect/download/viewer/# 

## 安装VNC Server

```
yum -y install tigervnc-server tigervnc  # 安装VNC Server及Viewer
```

## 配置VNC Server

VNC Server支持多种配置：

- 单用户单界面配置（一个用户访问，使用一个界面）
- 多用户单界面配置（多个用户访问，使用同一个界面）
- 多用户多界面配置（多个用户访问，使用各自的界面）

### 创建和修改配置文件（单用户单界面）

```
# cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@.service

# ll /etc/systemd/system/vncserver@.service
 -rw-r--r-- 1 root root 1828 Jun 21 11:08 /etc/systemd/system/vncserver@.service
# 
# vim /etc/systemd/system/vncserver@.service
# cat /etc/systemd/system/vncserver@.service  |grep -v "#"

[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking


ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i -geometry 1280x1024"
PIDFile=/home/<USER>/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

[Install]
WantedBy=multi-user.target
```

注意

- 启动服务的用户设置为root，这样VNC Client访问时可以看到菜单栏(Menu bar)
- 用户使用的是<USER>，联结时将登陆到<USER>的界面

本例只介绍单用户单界面配置。

