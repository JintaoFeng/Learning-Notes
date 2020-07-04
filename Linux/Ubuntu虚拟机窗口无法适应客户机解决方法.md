相信很多人在装虚拟机的时候，遇到了窗口过小不能自适应的问题。我也是查了好多资料，都说安装Vmware Tools即可解决，还有说修改分辨率也可以。两种方法亲测无效。

Vmware Tools工具官方已经不建议。更提倡开源的open-vm-tools，所以我安装了这个：

```
sudo  apt-get install open-vm-tools
```

第二步（关键在这一步）,推测是安装了一些依赖包：

```
sudo apt-get install open-vm*  
```

