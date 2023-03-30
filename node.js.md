官方网址

https://nodejs.org/en/

常用命令

```bash
访问nodejs官网（https://nodejs.org/en/）,选择合适的版本安装，node安装时自带npm
查看版本号：
node -v
npm -v
更新npm到最新版本
npm -g install npm
指定安装的版本
npm -g install npm@xxx
// 将npm设置为淘宝镜像
npm config set registry https://registry.npm.taobao.org 
// 全局切换官方镜像源
npm config set registry http://www.npmjs.org
// 查看镜像源使用状态
npm get registry 
// 镜像管理工具nrm
npm install -g nrm
//查看可切换的镜像源
nrm ls
// 切换淘宝镜像源
nrm use taobao 
```



