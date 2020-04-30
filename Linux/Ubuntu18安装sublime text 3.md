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

