# Ubuntu server安装中文字体

1. 检查是否安装了中文包支持

```shell
sudo dpkg -l | grep language-pack-zh-hans
```

2. 安装

如果没有安装，就安装，已经安装的就直接跳到下一步，原来的`language-pack-zh`已经更新为`language-pack-zh-hans`，所以这一步改了一下：

```shell
sudo apt-get install language-pack-zh-hans
```

3. 配置语言环境变量

```shell
sudo vim /etc/environment
#把下面代码添加进environment文件
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh:en_US:en"
```

4. 打开`/var/lib/locales/supported.d/local`，文件不在就创建文件

```shell
sudo vim /var/lib/locales/supported.d/local
#添加zh_CN.GB2312字符集
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_CN.GBK GBK
zh_CN GB2312
```

5. 保存文件，执行以下命令

```shell
sudo local-gen
```

6. 设置系统默认语言

打开`/etc/default/locale`

```shell
sudo vim /etc/default/locale
```

修改为：

```shell
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh:en_US:en" 
sudo reboot
```

然后重启终端。