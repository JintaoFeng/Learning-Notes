在Ubuntu的终端下，用`apt-get install`安装软件的时候，如果在未完成下载的情况下将终端关闭，此时`apt-get`进程可能没有结束。如果再次运行`apt-get install`命令安装，就会发生下面的提示：

![](..\picture\微信截图_20200430102806.png)

1.  强制解锁，命令：

```
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
```

2.  终端输入`ps aux`列出进程。找到含有`apt-get`的进程，直接`sudo kill PID`。

两种办法，都可解决。