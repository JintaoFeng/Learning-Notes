# 无线网卡的WiFi模式

今天用树莓派连接家里的路由器，然后电脑ssh连接的时候，发现ssh非常卡。经过多方查找，发现WiFi设置默认把节能模式打开。关闭节能模式就好。

```

```

![image-20210111233123872](E:\学习笔记\picture\image-20210111233123872.png)

运行如图所示：

power management 电源管理模式，on表示打开，off表示关闭。

Link Quality 连接质量，51/70表示70个测试连接数中有51个有效连接
Singal level 信号等级，信号值永远小于0，数值越大，信号越强，信号强度大于-65 dBm，可以满足机器正常运行。

关闭电源管理模式：

```
iw dev wlan0 get power_save 
Power save: on
sudo iw dev wlan0 set power_save off
```

修改完以后，会发现连接稳定许多，起码ssh的时候不会卡顿了。