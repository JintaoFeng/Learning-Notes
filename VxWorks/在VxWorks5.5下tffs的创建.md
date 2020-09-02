## 在VxWorks5.5下tffs的创建

硬件平台：基于MPC7145的mvme6100VME板卡。

软件：VxWorks5.5。

​		TFFS，True Flash File System的简写，是Vxworks在系统flash设备上构造的一个基于DOS的文件系统，用于存放交换及应用程序，bootrom程序等很少需要修改的信息，便于程序的更新和升级。

​		首先，在板卡上烧录支持tffs的bootrom，并在ftp目录下放置相应的image。在shell中使用以下命令查看tffs是否存在。

```
-> tffsShowAll
TFFS Version 2.2
0: socket=SIMM: type=0x0, unitSize=0x40000, mediaSize=0x2000000
1: socket=SIMM: type=0x0, unitSize=0x40000, mediaSize=0x2000000
value = 48 = 0x30 = '0'
-> 
```

​		shell返回以上字符后说明目前的bootrom和image支持tffs系统的创建。接下来需要格式化flash。

```
-> sp sysTffsFormat,0
task spawned: id = 1df3bc00, name = s1u0
value = 502512640 = 0x1df3bc00
-> 
```

​		等待格式化完成后，再创建tffs盘符。

```
-> usrTffsConfig(0,0,"/tffs0")
value = 0 = 0x0
-> 
```

​		此时，我们就可以在VxWorks系统设备中查找到`tffs0`目录。

```
-> devs
drv name
  0 /null
  1 /tyCo/0
  1 /tyCo/1
  5 host:
  8 /vio
  3 /tffs0
value = 0 = 0x0
-> 
```

```
-> cd "/tffs0"
value = 0 = 0x0
-> ls
value = 0 = 0x0
-> 
```

​		通过`cd`命令进入到“tffs0”盘里，可以通过`ls`命令列出目前盘里存在的文件。

在前面列出的设备里，“host:”代表ftp指向主机的目录，在”host：“目录下我们就能查看到ftp目录里的文件。

```
-> cd "host:"
value = 0 = 0x0
-> ls
LICENSE.TXT
LaserData.txt
N1225.out
README.TXT
SETUP
bootrom.bin
docs
host
lmPhoneNL.txt
setup.log
setup.log.abort
share
target
vxWorks
value = 0 = 0x0
```

​	这时可以从ftp目录拷贝文件到“tffs0”目录里，这样就可以实现主机个板卡之间文件的交互、程序的更新等。

```
-> pwd  
host:
value = 6 = 0x6
-> copy "vxWorks","/tffs0/vxWorks"
Copy OK: 2503526 bytes copied
value = 0 = 0x0
-> ls "/tffs0"
/tffs0/vxWorks 
value = 0 = 0x0
-> 
```

  接下来，我们可以通过修改VxWorks的启动设置，使系统启动的时候从tffs上引导，不用从ftp引导。

重启之后按照如下步骤修改：

```
Press any key to stop auto-boot...
 7

[VxWorks Boot]: c

'.' = clear field;  '-' = go to previous field;  ^D = quit

boot device          : geisc0 tffs0=0
processor number     : 0 
host name            : host 
file name            : vxWorks /tffs0/vxWorks
inet on ethernet (e) : 192.168.2.61 
inet on backplane (b): 
host inet (h)        : 192.168.2.202 
gateway inet (g)     : 
user (u)             : mvme6100 
ftp password (pw) (blank = use rsh): 1234 
flags (f)            : 0x0 
target name (tn)     : 
startup script (s)   : 
other (o)            : geisc0 

[VxWorks Boot]: @

boot device          : tffs=0
unit number          : 0 
processor number     : 0 
host name            : host
file name            : /tffs0/vxWorks
inet on ethernet (e) : 192.168.2.61
host inet (h)        : 192.168.2.202
user (u)             : mvme6100
ftp password (pw)    : 1234
flags (f)            : 0x0 
other (o)            : geisc0

Attaching to TFFS... done.
Loading /tffs0/vxWorks...1627776
Starting at 0x100000...

Attached TCP/IP interface to geisc unit 0
Attaching interface lo0...done

Adding 4120 symbols for standalone.
 

 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]
      ]]]]]]]]]]]  ]]]]     ]]]]]]]]]]       ]]              ]]]]         (R)
 ]     ]]]]]]]]]  ]]]]]]     ]]]]]]]]       ]]               ]]]]            
 ]]     ]]]]]]]  ]]]]]]]]     ]]]]]] ]     ]]                ]]]]            
 ]]]     ]]]]] ]    ]]]  ]     ]]]] ]]]   ]]]]]]]]]  ]]]] ]] ]]]]  ]]   ]]]]]
 ]]]]     ]]]  ]]    ]  ]]]     ]] ]]]]] ]]]]]]   ]] ]]]]]]] ]]]] ]]   ]]]]  
 ]]]]]     ]  ]]]]     ]]]]]      ]]]]]]]] ]]]]   ]] ]]]]    ]]]]]]]    ]]]] 
 ]]]]]]      ]]]]]     ]]]]]]    ]  ]]]]]  ]]]]   ]] ]]]]    ]]]]]]]]    ]]]]
 ]]]]]]]    ]]]]]  ]    ]]]]]]  ]    ]]]   ]]]]   ]] ]]]]    ]]]] ]]]]    ]]]]
 ]]]]]]]]  ]]]]]  ]]]    ]]]]]]]      ]     ]]]]]]]  ]]]]    ]]]]  ]]]] ]]]]]
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]]       Development System
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]]
 ]]]]]]]]]]]]]]]]]]]]]]]]]]]       VxWorks version 5.5.1
 ]]]]]]]]]]]]]]]]]]]]]]]]]]       KERNEL: WIND version 2.6
 ]]]]]]]]]]]]]]]]]]]]]]]]]       Copyright Wind River Systems, Inc., 1984-2003

                               CPU: Emerson MVME6100-0163 - MPC 7457.  Processor #0.
                              Memory Size: 0x20000000.  BSP version 1.2/8.
                             WDB Comm Type: WDB_COMM_END
                            WDB: Ready.
-> 
```

​		从启动信息可以看出，系统是从tff盘引导启动的。

​		我们也可以把程序放入tffs盘里，通过启动脚本使板卡在上电后自动引导程序，达到上电后程序立即启动的目的。

​		向`tffs0`盘中拷入启动脚本`start`，程序文件`MotionPPC.out`，这写名字根据自己程序的实际情况填写就可以，然后修改系统启动参数。

```
-> ls
vxWorks 
MotionPPC.out 
start 
value = 0 = 0x0
```



```
[VxWorks Boot]: c

'.' = clear field;  '-' = go to previous field;  ^D = quit

boot device          : tffs=00 
processor number     : 0 
host name            : host 
file name            : /tffs0/vxWorks 
inet on ethernet (e) : 192.168.2.61 
inet on backplane (b): 
host inet (h)        : 192.168.2.202 
gateway inet (g)     : 
user (u)             : mvme6100 
ftp password (pw) (blank = use rsh): 1234 
flags (f)            : 0x0 
target name (tn)     : 
startup script (s)   : /tffs0/start
other (o)            : geisc0 
```

​		重点是`startup script（s)`和`other(o)`将他们修改为`/ttds0/start`和`geisc0`，这样就可以使系统启动后先运行`tffs0`盘里的`start`文件，并且在程序里还可以使用`geisc0`网络。启动完成如下。

```
Executing startup script /tffs0/start ...
cd "/tffs0/"
value = 0 = 0x0
ld< MotionPPC.out
value = 502365328 = 0x1df17c90
TaskInit
Listen socket create successfully!
Bind to port successfully!
Listening!
value = 488246816 = 0x1d1a0e20
cd "host:"
value = 0 = 0x0
5000

Done executing startup script /tffs0/start
```

​		start文件的程序内容如下：

```
cd "/tffs0/"
ld< MotionPPC.out
TaskInit
cd "host:"
```

​		启动脚本很简单，第一行是切换到`/tffs0`盘符里，然后加载程序文件，再执行程序文件的中的函数`TaskInit`,最后再切换到ftp目录下，`host:`指的是ftp目录，也就是我们在用ftp启动时指定的目录下，程序启动的时候默认都是`host:`目录。

​	通过创建`/tffs`文件系统，使板卡的flash设备操作和我们平时使用PC机一样方便，我们可以对板卡的文件很方便的进行改动。在程序里使用C语言的文件库就可以对文件进行改动。并且可以把程序中需要永久保存的配置文件放入`tffs`中，使程序启动的时候更加方便。

