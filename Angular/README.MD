## 说明
本文件夹下面的项目，根据 https://angular.cn/ 进行操作


## 快速开始
#### 全局安装 Angular CLI
` npm install -g @angular/cli  `

#### 创建工作空间和初始应用
下面的命令将会创建一个初始引用项目，并通过命令行提示你要把哪些特性包含在初始的应用项目中（如路由、使用的样式文件类型）
` ng new my-app `

创建出来
- 一个新的工作空间，根目录名叫my-app
- 一个初始的骨架应用项目，也叫my-app（位于scr子目录下）

#### 启动开发服务器
- 进入工作空间目录 ` cd my-app `
- 使用CLI命令 ` ng serve ` 启动开发服务器，并带上 ` --open ` 选项
注： ` ng serve ` 命令会自动开发服务器，并监视文件的变化，相当于热更新
     ` --open ` 或  ` -o ` 选项会自动打开浏览器，并访问 http://localhost:4200/
     
     
#### 根组件 app-root
- 位于 ./src/app/app.component.ts



