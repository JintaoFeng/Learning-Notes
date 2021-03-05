# Ubuntu20 server 添加无线

网上连接wifi的蛇者有很多，大多都是设置`wpa_supplicant.conf`,但是我在Ubuntu20上经过尝试，发现并没有什么用。最后通过如下设置解决。

系统：Ubuntu20.04LTS server

1. 进入netplan文件夹

```
cd /etc/netplan/
```

2. 修改网络配置文件：

```
sudo vim 50-cloud-init.yaml
```

![image-20210111232033332](..\picture\image-20210111232033332.png)

注意在冒号后面，要有空格或换行。

3. 启用配置：

```
sudo netplan try
sudo netplan apply
systemctl daemon-reload
```

如果配置写的有问题，`sudo netplan try`的时候会报错，注意以下格式。一般是少了空格，少了双引号，多了中括号之类的格式错误