## settings.json全局配置
```json
{
    //vscode的默认终端,此处设置为cmd
    "terminal.integrated.shell.windows":"C:\\WINDOWS\\System32\\cmd.exe",

    //拖拽移动文件时不要确认提示
    "explorer.confirmDragAndDrop": false,

    //手动升级vscode
    "update.mode": "manual",

    //自动保存,此处设置为永远自动保存
    "files.autoSave": "afterDelay",

    //task的下拉列表中显示历史常用的个数
    "task.quickOpen.history": 0,
}
```
## tasks.json
```json
{

    "version": "2.0.0",
    "command": "g++",
    "args": ["-g","${file}","-o","${fileBasenameNoExtension}.exe"], // 编译命令参数
    "problemMatcher": {
        "owner": "cpp",
        "fileLocation": ["relative", "\\"],
        "pattern": {
            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",

            "file": 1,
            "line": 2,
            "column": 3,
            "severity": 4,
            "message": 5
        }
    }
}

{
    //在task列表中显示的文字
    "label": "make file",
    //运行环境
    "type": "shell",
    //命令
    "command": "make",
     //参数

    "args": [
    ],
    //所述属组
    "group": "build",
    //报错处理
    "problemMatcher": []
}

 //删除文件
 {
    "label": "Delete FILE",
    "type": "shell",
    "command": "del",
    "args": [
        "*.aux",
        "*.log",
        "*.dvi",
        "*.pdf"
    ],

    //设置为none的task不会在build task的下拉列表中展示
    "group": "none",
    "problemMatcher": []
 },

  //将生成的eps移动至相应文件夹

 {
    "label": "Move EPS",
    "type": "shell",
    "command": "move",
    "args": [
        "/Y",
        //$开头的都是vscode提供的封装好的变量,具体内容
        //可以自己尝试
        "${workspaceFolder}\\${fileBasenameNoExtension}.eps",
        "C:\\Pt_Latex\\Reources\\"
    ],

    "group": "build",
    //在运行这个任务之前,需要提前运行的任务
    "dependsOn": "Delete FILE"
    "problemMatcher": []
}
```

## launch.json
```json
{
    "name": "(gdb) Launch", 
    // 配置名称，将会在启动配置的下拉菜单中显示
    "type": "cppdbg", 
    // 配置类型，这里只能为cppdbg
    "request": "launch", 
    // 请求配置类型，可以为launch（启动）或attach（附加）

    "program": "${workspaceFolder}/${fileBasenameNoExtension}.exe",
    // 将要进行调试的程序的路径
    "args": [], 
    // 程序调试时传递给程序的命令行参数，一般设为空即可
    "stopAtEntry": false, 
    // 设为true时程序将暂停在程序入口处，一般设置为false

    "cwd": "${workspaceFolder}", 
    // 调试程序时的工作目录，一般为${workspaceFolder}即代码所在目录
    "environment": [],
    "externalConsole": true, 
    // 调试时是否显示控制台窗口，一般设置为true显示控制台
    "MIMode": "gdb",
    "miDebuggerPath": "C:\\apps\\MinGW\\bin\\gdb.exe", 
    // miDebugger的路径，注意这里要与MinGw的路径对应
    "preLaunchTask": "g++", 
    // 调试会话开始前执行的任务，一般为编译程序，c++为g++, c为gcc

    "setupCommands": [
    {
        "description": "Enable pretty-printing for gdb",
        //为gdb启用整齐打印
        "text": "-enable-pretty-printing",
        "ignoreFailures": true
    }
    ]

}
```
