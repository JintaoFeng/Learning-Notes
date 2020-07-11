*   backtrace(bt) 查看各级函数调用及参数
*   finish  执行到当前函数返回，然后停下来等待命令
*   frame(f)  帧编号   选择栈帧
*   info(i) locals   查看当前栈帧局部变量的值
*   list(l) 列出源代码，接着从上次的位置往下列，每次列10行
*   list 行号   列出从第几行开始的源代码
*   list 函数名 列出每个函数的源代码
*   next(n)  执行下一行语句
*   print(p) 打印表达式的值，通过表达式可以修改变量的值或者调用函数
*   set var 修改变量的值
*   start 开始执行程序，停在main函数第一行语句前面等待命令
*   step(s) 执行下一行语句，如果有函数调用则进入到函数中
*   break(b) 行号  在某一行设置断点
*   break 函数名   在某个函数开头设置断点
*   break...if...  设置断点条件
*   continue(c)  从当前位置开始连续而非单步执行程序
*   delete breakpoints  删除断点
*   display 变量名   跟踪查看一个变量，每次停下来都显示它的值
*   undisplay   取消对先前设置的那些变量的跟踪
*   disable breakpoints 禁用断点
*   enable breakpoints 启用断点
*   info(i) breakpoints 查看当前设置了哪些断点
*   run(r) 从头开始连续而非单步执行程序
*   watch 设置观察点，观察点是当程序访问某一存储单元时中断
*   info(i) watchpoints 查看当前设置了哪些观察点
*   x 从某个位置开始打印存储器的一段内容，全部当成字节来看，而不区分哪些字节属于哪些变量
*   