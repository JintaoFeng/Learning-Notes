# Linux创建桌面快捷方式

 默认情况下系统所有的快捷方式放在/usr/share/applications，打开该目录会看到大量的.desktop文件，每个文件便是一个快捷方式。 但是有的程序不会在这里创建快捷方式。

如果想让某个程序图标显示在桌面上，只需将程序相应.desktop文件复制在桌面上就行了。

当然，如果想图标出现在启动器里，就把图标文件复制到上述文件夹里。

那么如何自己制作一个桌面图标呢。

先看一下.desktop文件格式

```
[Desktop Entry]               # 每个desktop文件都已这个标签开始，说明这是一个Desktop Entry 文件.
Version = 1.0                 # 标明Desktop Entry的版本(可选).
Name=Firefox                  # 程序名称(必须),这里是以创建一个eclipse的快捷方式为例.
GenericName = Web Browser     # 程序通用名称(可选).
Comment = Browse the Web      # 程序描述(可选).
Exec = firefox %u             # 程序的启动命令(必选),可以带参数运行
                              #+当下面的Type为Application，此项有效
Icon = firefox                # 设置快捷方式的图标(可选).
Terminal = false                    # 是否在终端中运行(可选),当Type为Application,此项有效.
Type = Application                  # desktop的类型(必选),常见值有“Application”和“Link”.
Categories = GNOME;Application;Network;   # 注明在菜单栏中显示的类别(可选)      
```

现在，我有一个eclipse程序文件夹为/usr/local/eclipse

启动命令的绝对路径为/usr/local/eclipse/eclipse

图标文件位置为/usr/local/eclipse/icon.xpm

 首先，桌面建立一个名为eclipse.desktop的文件，然后内容如下填写 

```
[Desktop Entry]    
Version = 1.0           
Name=eclipse
Exec =/usr/local/eclipse/eclipse
Icon =/usr/local/eclipse/icon.xpm
Terminal = false
Type = Application
```

 最后保存就行了。

还有别忘了给予这个文件可执行的权限

## 问题记录

 创建linux桌面快捷启动方式启动提示”untrusted application launcher“，且无信任确认 

**原因分析：**用户权限的问题，新版本的Gnome桌面快捷方式如果默认是root，则不让启动。

**解决方案：**把文件权限设置为普通用户权限，启动时使用root密码确认即可解决。
修改文件权限指令：