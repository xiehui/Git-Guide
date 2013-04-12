## Git安装及配置
### 命令模式git for windows
#### git安装
 下载[msysGit](https://code.google.com/p/msysgit/downloads/list)或github For windows，依照提示进行安装。
#### git配置
* 使用Git的第一件事就是设置你的名字和email,这些就是你在提交commit时的签名

      $ git config --global user.name "xiehui"
      
      $ git config --global user.email "xiehui.fj@gmail.com"

这样的设置会在你的主目录(home directory)建立一个叫 ~/.gitconfig 的文件，会影响此用户建立的每个项目.

