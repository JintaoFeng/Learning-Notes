# CentOS 7创建本地yum源

**使用nginx+createrepo搭建本地yum源。**

## 准备工作

1. 软件库文件：

![1646016670249](..\picture\1646016670249.png)

2. 安装createrepo。

![1646016792172](..\picture\1646016792172.png)

3. 安装nginx.从网上下载一个rpm安装包，安装。

![1646026633004](..\picture\1646026633004.png)

4. 启动nginx服务。

```
systemctl start nginx
```

在浏览器输入网址，可以看到正常访问nginx服务。

![1646039868782](..\picture\1646039868782.png)

5. 修改/etc/nginx/conf.d/default.conf文件。

![1646127518829](..\picture\1646127518829.png)

重启nginx服务，从浏览器访问可见如下：

```
systemctl restart nginx
```



![1646127571998](..\picture\1646127571998.png)

6. 创建安装包索引文件。

在安装包的目录文件中使用`createrepo`指令创建索引。

![1646128481547](..\picture\1646128481547.png)

执行完成后会在相应的文件夹中找到repodata文件夹，索引数据会存放在里面。

7. 配置yum客户端

  yum 源配置文件位于 /etc/yum.repos.d/ 目录下，文件扩展名为"*.repo"（只要扩展名为 "*.repo" 的文件都是 yum 源的配置文件）。  通常情况下 CentOS-Base.repo 文件生效。 

![1646128635524](..\picture\1646128635524.png)

进入CentOS-Base.repo文件。

![1646128791146](..\picture\1646128791146.png)

容器中各参数的含义分别为：

- [base]：容器名称，一定要放在[]中。
- name：容器说明，可以自己随便写。
- mirrorlist：镜像站点，这个可以注释掉。
- baseurl：我们的 yum 源服务器的地址。默认是 CentOS 官方的 yum 源服务器，是可以使用的。如果你觉得慢，则可以改成你喜欢的 yum 源地址。
- enabled：此容器是否生效，如果不写或写成 enabled 则表示此容器生效，写成 enable=0 则表示此容器不生效。
- gpgcheck：如果为 1 则表示 RPM 的数字证书生效；如果为 0 则表示 RPM 的数字证书不生效。
- gpgkey：数字证书的公钥文件保存位置。不用修改。

![1646129094931](..\picture\1646129094931.png)

8. 客户端repo列表查看。`yum repolist`

![1646129299927](..\picture\1646129299927.png)

9. 测试

![1646129394806](..\picture\1646129394806.png)

10. 后期维护

 有时候你的软件仓库中含有很多的软件包，虽然只是其中的一小部分有所改动但是你却不必为每个软件包重新产生元数据，这样做明显太浪费时间了。这就是--update 选项诞生的原因。
  像之前一样运行 createrepo 命令但是添加了--update 选项. 

```
createrepo --update  /my_repo
```

客户端需要做一次更新

```
yum clean all    #清除原有的yum信息
```



### 问题分析

如果在浏览器访问nginx服务时出现403错误，可能会是以下原因导致。

nginx的 403 Forbidden errors 表示你在请求一个资源文件但是nginx不允许你查看。
403 Forbidden 只是一个HTTP状态码，像404,200一样不是技术上的错误。
哪些场景需要返回403状态码的场景？

1.网站禁止特定的用户访问所有内容，例：网站屏蔽某个ip访问。
2.访问禁止目录浏览的目录，例：设置autoindex off后访问目录。
3.用户访问只能被内网访问的文件。

![1646039003007](..\picture\1646039003007.png)

1. 权限问题。查看nginx服务登录的用户是否有访问nginx根目录的权限。把/etc/nginx/nginx.conf中启动用户更改为目录的可访问用户即可。

![1646098012993](..\picture\1646098012993.png)

2. SELinux设置为开启状态。

首先查看SELinux的开启状态，如果SElinux status参数为enabled即为开启状态。

![1646113554612](..\picture\1646113554612.png)

将status设置为disabled即可。有两种方法：

临时关闭

```
setenforce 0
```

永久关闭

```
vim /etc/selinux/config
将SELINUX=enforcing改为SELINUX=disabled
```

3. 缺少index.html文件，就是配置文件中 index index.html index.htm这行中的指定的文件。 



