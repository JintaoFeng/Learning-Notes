## sublime text3 的安装

```

wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt-get update
sudo apt-get install sublime-text
```

依次执行以上命令，等待安装完成即可。

接下来是解决在Ubuntu下sublime不能输入中文的问题！
按顺序执行以下命令！

```
git clone https://github.com/lyfeyaj/sublime-text-imfix.git
cd sublime-text-imfix && ./sublime-imfix
```

## sublime text 3 的汉化

打开sublime的软件，在菜单栏中，找到`Preference`,在弹出的下拉菜单栏中找到`Package Control`

![](..\picture\微信截图_20200430122700.png)

![](..\picture\微信截图_20200430122835.png)

在打开的命令行里搜索`install Package`,打开它，等待搜索完成，自会弹出对话框，在搜索框中输入`chinese`,然后在下拉菜单中找到`chineseLocalizations`,点击安装。

![](..\picture\微信截图_20200430123310.png)

等待系统安装完即可。

系统安装完自然会将英文菜单栏切换到中文界面，如下图所示：

![](..\picture\微信截图_20200430123646.png)

如果想要切换回英文，可以在`help`中找到`Language`选项，在下拉菜单中选择相应语言即可.

![](..\picture\微信截图_20200430123919.png)