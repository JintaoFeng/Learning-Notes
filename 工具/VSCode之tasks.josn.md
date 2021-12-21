````json
{
"version": "2.0.0",
    "tasks": [
        {
			
			"type": "cppbuild",
			"label": "cmake",
			"command": "cmake",
			"args": [
				".."
			],
			"options": {
				"cwd": "${workspaceFolder}/build"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": "build",
			"detail": "cmake: cmake .."
		},
		{
			"dependsOn":"cmake",
			"type": "cppbuild",
			"label": "make",
			"command": "make",
			"args": [
				
			],
			"options": {
				"cwd": "${workspaceFolder}/build"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": "build",
			"detail": "make: make.exe"
		},
		{
			"dependsOn":"make",
			"type": "cppbuild",
			"label": "run",
			"command": "main.exe",
			"args": [
				
			],
			"options": {
				"cwd": "${workspaceFolder}/bin"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": "build",
			"detail": "run: main.exe"
		}
    ]
}
````

ca'm'k