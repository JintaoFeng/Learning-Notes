# tornado打开时出错 svctcp_.c -tcp socket creation problem

以前用的都好着的，今天突然就出现这个问题了，是vxworks

tclsocket提示：bai无法找到序数 无法定位序数xxxx于动态链接库mfc42.dll上。du
（问题的本质zhi：svctcp_.c tcp socket creation problem）
解决方法：
删除 安装目录下dao\host\x86-win32\bin目录下的mfc42.dll,让tornado使用系统目录下的mfc42.dll （其中c:\i386下应该有mfc42.dll）