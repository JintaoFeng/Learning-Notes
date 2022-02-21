# Centos 7 安装MySQL5.7

## 准备安装包

MySQL安装包分为两种，一种是单独rpm package，另一种是rpm bundle。

![image-20220213190708660](..\picture\image-20220213190708660.png)

下载网址为：[MySQL :: Download MySQL Community Server (Archived Versions)](https://downloads.mysql.com/archives/community/)

选择自己需要的安装包进行下载。

![image-20220215113844728](..\picture\image-20220215113844728.png)

安装之前先查询是否有安装相关软件包：

```
rpm -qa|grep mysql
```

Linux默认安装mariadb包,会和mysql冲突，：

```
rpm -qa|grep mariadb
```

卸载之前安装软件包：

```
rpm -e --nodeps mysql-libs-5.1.73-1.el6.x86_64
rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
```

解压后文件如上所示，依次安装上述文件：

```
# rpm -ivh mysql-community-common-8.0.12-1.el6.x86_64.rpm

# rpm -ivh mysql-community-libs-8.0.12-1.el6.x86_64.rpm

# rpm -ivh mysql-community-libs-compat-8.0.12-1.el6.x86_64.rpm

# rpm -ivh mysql-community-client-8.0.12-1.el6.x86_64.rpm

# rpm -ivh mysql-community-server-8.0.12-1.el6.x86_64.rpm
```



修改配置`/etc/my.cnf`。添加如下变量。

```
[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_bin
init_connect=’SET NAMES utf8’
```

安装完先初始化：

```
mysqld --initialize --user=mysql
```

启动服务：

```
# service mysql start
```

安装mysql后第一次启动使用默认密码，可以在mysqld.log中查询：

```
cat mysqld.log|grep pass
```

关闭服务:

```
systemctl stop mysqld.service
```

查看服务状态

```
systemctl status mysqld
```

查看是否自启动:

```
systemctl list-unit-files|grep mysqld.service
```

设置自启动：

```
systemctl enable mysqld.sercice
```

首次登录：

首次登陆通过 “mysql -uroot -p” 进行登录，在 “Enter password：” 后输入初始化密码。正确输入密码后输出如下所示则表明成功连接数据库：

![img](..\picture\9d4f44d5f5dd51f61a28efde52feafc2.png)

因为初始化密码默认是过期的，必须修改新密码后才能正常使用数据库：

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
root 指的是mysql用户名
localhost代表本账户只能本地登录数据库，无法远程连接。
```

注意：密码太简单可能会报以下错：

```
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

如果想设置简单的密码，可以根据自己需要来设置以下参数：

```
set global validate_password_policy=LOW; // 设置密码的验证强度等级为低(LOW)
set global validate_password_length=6; // 设置密码长度为6，最小为4
```

输入以下语句可以发现数据库和服务端的默认字符都是latin1，如果不修改容易出现乱码：

```
show variables like 'character%';
```

![img](..\picture\1bc64ba1337bc703a2dca57ad31884c7.png)

注意数据库和服务端的默认字符都是latin1，如果不修改容易出现乱码：

在安装初始已经修改过配置文件，如果查询结果不对查一下配置文件设置是否正确。

输入以下语句查看MySQL的用户信息：

![img](..\picture\91406878f752fd41a53884bb8533fda5.png)

```
host：表示连接类型

%：表示所有远程通过 TCP方式的连接
IP地址：如 (192.168.1.2,127.0.0.1) 通过制定ip地址进行的TCP方式的连接
机器名：通过制定i网络中的机器名进行的TCP方式的连接
::1：IPv6的本地ip地址 等同于IPv4的 127.0.0.1
localhost：本地方式通过命令行方式的连接 ，比如mysql -u xxx -p 123xxx 方式的连接。
user：表示用户名

同一用户通过不同方式链接的权限是不一样的。

authentication_string：密码

所有密码串通过password (明文字符串)生成的密文字符串。加密算法为MYSQLSHA1，不可逆。MySQL 5.7的密码保存到authentication_string字段中不再使用password字段(在5.5中使用)。

```

- 用户授权命令(该命令执行授权时如果发现没有指定的用户，则会直接创建一个新用户来完成授权)：

```
grant 权限1,…权限n on 数据库名.表名 to 用户名@用户地址 identified by ‘密码’;
```

例如，授予通过网络方式登录的root用户，有对所有库、所有表的全部权限，密码设为”newpwd123”：

```
grant all privileges on *.* to root@'%' identified by 'newpwd123';
```

- 创建用户，例如名为root的用户，密码设为123abc：

```
create user root identified by '123abc';
```

- 修改用户名

```
update mysql.user **set** user='li4' where user='wang5';
flush privileges;  # 所有通过user表的修改，必须用该命令才能生效。
```

- 删除用户

```
drop user user@host;
```

- 修改当前用户的密码

```
set password = password('new_password')
```

- 修改某个用户的密码

```
update mysql.user set password=password('new_password') where user='name';
flush privileges; # 所有通过 user表的修改，必须用该命令才能生效。
```

|      |      |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |

#### 假如在mysql初始化过程中遇到以下错误：

![image-20210412150522732](..\picture\246ec3c375fb3c6539b9570c2d27cb7b.png)

看报错信息里说 " --initialize specified but the data directory has files in it. Aborting. "，说明mysql的数据目录已经存在了。可能是之前安装过mysql，卸载的时候没卸干净，没有连着数据目录一起删除。

最简单的解决方法

已知MySQL的数据目录默认是 /var/lib/mysql，则我们可以对它进行修改：

终端输入 " vim /etc/my.cnf " 对配置文件进行编辑，修改datadir为自己想存放数据的路径。

![img](..\picture\944d9b35bdea737d7f81561b2a1fe071.png)

##### 无法远程连接MySQL服务

检查本机防火墙，系统默认关闭端口。

查看端口：

```
sudo firewall-cmd --zone=public --query-port=3306/tcp
sudo firewall-cmd --zone=public --list-ports
```

添加端口：

```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

重新载入：

```
firewall-cmd --reload
```









mysql  在安装confluence时的配置文件：

```
[mysqld]
character-set-server = utf8 #设置数据库服务端的字符编码为UTF8，否则在后面导入数据后就会出现乱码的情况，默认的字符编码为latin1
collation-server = utf8_bin #指定数据集的排序方式，常用的是utf8_general_ci(忽略大小写)，utf8_bin(二进制存储，区分大小写)
default-storage-engine = InnoDB #mysql5.6的默认存储引擎是InnoDB，可以不写该行
transaction_isolation = READ-COMMITTED #默认事务隔离级别是repeatable-read，这个配置必须有，否则后面的页面设置数据库部分会失败
max_allowed_packet = 256MB #单个记录的大小，如果大于该值，会导致写入数据库失败
innodb_log_file_size = 2GB #事务日志文件的大小，设置太大会导致数据库意外夯机的恢复时间会更长，设置太小，会影响InnoDB的性能
sql_mode = NO_AUTO_VALUE_ON_ZERO #影响AUTO_INCREMENT列的处理。一般情况，可以向该列插入NULL或0生成下一个序列号。NO_AUTO_VALUE_ON_ZERO禁用0，因此只有NULL可以生成下一个序列号。
binlog_format = row #MySQL复制的方式，如果mysql是单机则不需要配置
```

