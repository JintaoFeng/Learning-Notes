# 安装bitbucket

1. 安装JAVA环境。

下载JDK压缩包，将其解压到安装目录中。

```
ar -zxvf OpenJDK17U-jdk_x64_linux_hotspot_17.0.2_8.tar.gz  -C /opt/java
```

![1645172391695](..\picture\1645172391695.png)

解压完文件夹内容如上。

然后需要设置环境变量。

```
vi /etc/profile

在尾部添加：
export JAVA_HOME=/opt/java/jdk-17.0.2+8
export PATH=$JAVA_HOME/bin:$PATH


保存后重启
init 6
```

重启后检查java：

```
[root@localhost ~]# java -version
openjdk version "17.0.2" 2022-01-18
OpenJDK Runtime Environment Temurin-17.0.2+8 (build 17.0.2+8)
OpenJDK 64-Bit Server VM Temurin-17.0.2+8 (build 17.0.2+8, mixed mode, sharing)
```

2. 安装bitbucket环境

先安装git，共有两个安装包

```
rpm -ivh pcre2-10.23-2.el7.x86_64.rpm
rpm -ivh git224-core-2.24.4-1.el7.ius.x86_64.rpm
```

如果没有git。安装bitbucket时会有提示：

![1645174728268](..\picture\1645174728268.png)

```
[root@localhost cdrom]# ./atlassian-bitbucket-6.10.17-x64.bin 
Unpacking JRE ...
Starting Installer ...

Bitbucket 6.10.17 installation wizard
Would you like to install or upgrade an instance?
Install a new instance [1, Enter], Upgrade an existing instance [2]
1

Install Bitbucket 6.10.17
What type of instance are you looking to install?
Install a Server instance [1, Enter], Install a Data Center instance [2], Install a Smart Mirroring instance [3]
1
Where should Bitbucket be installed?

Select the folder where you would like Bitbucket 6.10.17 to be installed,
then click Next.
[/opt/atlassian/bitbucket/6.10.17]
/home/atlassian/bitbucket                 
Default location for Bitbucket home directory

The location for Bitbucket data.
This will be the default location for repositories, plugins, and other data.

Ensure that this location is not used by another Bitbucket installation.
[/var/atlassian/application-data/bitbucket]
/home/atlassian/application-data
Configure which ports Bitbucket will use.


Configure TCP Ports
Bitbucket requires a TCP port that isn't being used by other applications.

The HTTP port is where users access Bitbucket through their browsers.

Bitbucket also requires ports 7992 and 7993 are available to run an embedded
Elasticsearch instance that provides search functionality to Bitbucket.
HTTP Port Number
[7990]



Run as a service
For a production server we recommend that you run Bitbucket as a
Windows/Linux service because Bitbucket will restart automatically when the
computer restarts.
Install Bitbucket as a service?
Yes [y, Enter], No [n]
y
Please review your Bitbucket installation settings


Installation Summary
Installation Directory: /home/atlassian/bitbucket 
Home Directory: /home/atlassian/application-data 
HTTP Port: 7990 
Install as a service: Yes 

Install [i, Enter], Exit [e]
i

Extracting files ...
                                                                           

Installation of Bitbucket is complete
Would you like to launch Bitbucket?
Yes [y, Enter], No [n]
n

An important note about changes to starting Bitbucket Server
Bitbucket Server standard startup commands have changed to automatically
start a new service that enabled code search.When starting Bitbucket Server
manually, ensure you start all required services using both of the following
command:service atlbitbucket start

Installation of Bitbucket 6.10.17 is complete
Your installation of Bitbucket 6.10.17 is now ready and can be accessed via
your browser.
Bitbucket 6.10.17 can be accessed at http://localhost:7990
Finishing installation ...

```

安装jdbc驱动

将`mysql-connector-java-5.1.42-bin.jar`复制到`/home/atlassian/bitbucket/app/WEB-INF/lib`目录下.

```
[root@localhost lib]# cp /mnt/cdrom/mysql-connector-java-5.1.42-bin.jar ./
```

安装agent.

将`atlassian-agent.jar`安装到`/opt/java/agent`。配置环境变量。

```
[root@localhost java]# cp /mnt/cdrom/atlassian-agent.jar /opt/java/agent
vi /home/atlassian/bitbucket/bin/_start-webapp.sh
export JAVA_OPTS="-javaagent:/opt/java/agent/atlassian-agent.jar ${JAVA_OPTS}"
```

开放防火墙端口

```
[root@localhost bin]# firewall-cmd --permanent --zone=public --add-port=7990/tcp
success
[root@localhost bin]# firewall-cmd --reload
success
```

重启系统,然后启动服务;

```
init 6
systemctl start atlbitbucket
```

如果在启动的过程中遇到以下问题：

![1645180821653](..\picture\1645180821653.png)

找到`/home/atlassian/application-data/shared/search/jvm.options`文件进行修改。

```
[root@localhost home]# vi /home/atlassian/bitbucket/elasticsearch/config-template/jvm.options
```

```
#-XX:+UseConcMarkSweepGC
#-XX:CMSInitiatingOccupancyFraction=75
#-XX:+UseCMSInitiatingOccupancyOnly
将以上三句注释掉即可。
```

服务将启动后，在浏览器中登录`172.16.20.216:7990`即可.

