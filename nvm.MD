## node包管理工具 nvm
#### 安装
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.1/install.sh | bash

#### 检测是否安装成功
command -v nvm

#### 常用命令

列出所有版本：nvm ls-remote

安装指定版本：nvm install <version>，如nvm install v5.1

使用指定版本（修改PATH）：nvm use <version>

查看当前使用版本：nvm ls
