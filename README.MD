## 安装
#### homebrew
` ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" `

#### node
```
brew link node
brew uninstall node
brew install node
```

#### cnpm
` npm install -g cnpm --registry=https://registry.npm.taobao.org `

#### nvm
` curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash `

如果安装后 ` nvm `命令不存在
你的系统可能缺少一个 .bash_profile 文件，你可以创建一个此文件（可通过vi或vim命令），打开复制粘贴以下代码（安装nvm成功后终端的最好3行代码）进去，保存，然后再次运行安装命令；
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

再次运行安装命令

## nvm 常用命令
- nvm install stable ## 安装最新稳定版 node，当前是node v9.5.0 (npm v5.6.0)
- nvm install <version> ## 安装指定版本，可模糊安装，如：安装v4.4.0，既可nvm install v4.4.0，又可nvm install 4.4
- nvm uninstall <version> ## 删除已安装的指定版本，语法与install类似
- nvm use <version> ## 切换使用指定的版本node
- nvm ls ## 列出所有安装的版本
- nvm ls-remote ## 列出所有远程服务器的版本（官方node version list）
- nvm current ## 显示当前的版本
- nvm alias <name> <version> ## 给不同的版本号添加别名
- nvm unalias <name> ## 删除已定义的别名
- nvm reinstall-packages <version> ## 在当前版本 node 环境下，重新全局安装指定版本号的 npm 包

 
