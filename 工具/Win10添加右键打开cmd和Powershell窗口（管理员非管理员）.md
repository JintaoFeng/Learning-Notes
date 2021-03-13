# Win10添加右键打开cmd和Powershell窗口（管理员/非管理员）

## 通过编写.reg文件进行添加（推荐）

### 实现效果

- 右键：

> 在此处打开命令行窗口
>
> 在此处打开 Powershell 窗口

- shift + 右键：

> 在此处打开命令行窗口(管理员)
> 在此处打开 Powershell 窗口(管理员)



其中shift + 右键 实现“在此处打开 Powershell 窗口”为系统自带，不需要添加,但是需要修改。

下面代码里的3和4均是通过隐藏的PowerShell窗口来调起powershell(cmd)的，因此会闪过一次powershell窗口。打开的时候会有用户账户控制弹窗，以确认管理员身份，


### 具体代码

代码如下，Windows直接新建txt，粘贴进去保存，然后选择另存为，保存类型选所有文件、编码选ANSI、文件名为CmdAndPowershellAll.reg(名字无所谓，后缀为.reg就可以)。双击打开，会进行两次确认，然后会提示“已成功添加到注册表中”，这样就成功了！现在可以右键、shift+右键尝试一下了！

```reg
Windows Registry Editor Version 5.00
 
; 若原先有，先删除原来的
[-HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere]
[-HKEY_CLASSES_ROOT\Directory\Background\shell\runas]
[-HKEY_CLASSES_ROOT\Directory\Background\shell\PowershellAdmin]
 
; 1.右键：命令行
[HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere]
@=" 在此处打开命令行窗口 "
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere\command]
@="cmd.exe -noexit -command Set-Location -literalPath \"%V\"" 
 
; 2.shift+右键：命令行（管理员）
[HKEY_CLASSES_ROOT\Directory\Background\shell\runas]
@="在此处打开命令行窗口(管理员)"
"ShowBasedOnVelocityId"=dword:00639bc8
 "Extended"=""
[HKEY_CLASSES_ROOT\Directory\Background\shell\runas\command]
@="cmd.exe /s /k pushd \"%V\""
 
; 3.shift+右键：Powershell(管理员)
[HKEY_CLASSES_ROOT\Directory\Background\shell\PowershellAdmin]
@="在此处打开 Powershell 窗口(管理员)"
"Extended"=""
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\PowershellAdmin\command]
@="powershell.exe -windowstyle hidden -Command $stpath = pwd; Start-Process PowerShell -ArgumentList \\\"-NoExit\\\", \\\"-Command Set-Location -literalPath '%V'\\\" -verb RunAs"
 
; 4.设置右键 管理员打开cmd的另一种方法（可用来替换上面的2）
; 通过Powershell调起，会闪过一次Powershell的窗口，去掉下面几行的[; ]可以取消注释
; [-HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHereAdmin]
; 
; [HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHereAdmin]
; @="在此处打开命令行窗口(管理员)"
; 
; [HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHereAdmin\command]
; @="PowerShell -windowstyle hidden -Command \"Start-Process cmd.exe -ArgumentList '/s,/k, pushd,%V' -Verb RunAs\""
```

说明：

1.前面有分号;的是注释；

2.带有"Extended"=""的是shift+右键的，可以自行调整四个命令是否加这个；

3.cmd管理员有两种方法，一个是2的runas，一个是4的powershell调起，4（已注释掉）会闪过powershell窗口，所以没有采用。

## 其中shift + 右键 实现“在此处打开 Powershell 窗口”更改为右键实现“在此处打开 Powershell 窗口”。

在注册表\HKEY_CLASSES_ROOT\Directory\Background\shell\PowershellAdmin中如果效果是shift+右键的话会有Extended选项，只需将其删除就可以更改为右键实现。

shift+右键：

![image-20210306203130357](..\picture\image-20210306203130357.png)

右键：

![image-20210306203152975](..\picture\image-20210306203152975.png)

​			