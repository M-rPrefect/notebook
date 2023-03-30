Git

1.常用Linux命令

1. cd 选择目录
2. cd.. 后退一级
3. ls 显示当前文件夹包含的所有目录
4. pwd 显示当前所在的目录路径
5. touch 新建文件
6. rm 删除文件
7. clear 清屏
8. mkdir 新建一个文件夹
9. rm-r 删除一个文件夹
10. mv 移动，把一个文件移动到一个文件夹里  --mv index.html test
11. reset 重新初始化终端/清屏
12. history 查看命令历史
13. exit 退出
14. #注释

2.配置

​	1.获取当前所有的配置 git config -l

​	2.系统默认配置git config --system --list

​	3.git config --global --list 本地自己的配置

​	4.Git相关配置目录Git/etc/getconfig

​	5.C:/Users/Mr.Prefect/.gitconfig自己的配置

​		 git config --global user.name "chenhebin"
​		 git config --global user.email "2417423080@qq.com"

3.理论

四个模块

工作目录WorkingDirectory	{git add files,git checkout}

暂存区Stage	{git commit,git reset}

本地仓库Repository	{git push,git pull}

远程仓库RemoteDirectory	{git pull,git push}

3.1创建全新的仓库

​	git init

3.2克隆远程仓库

​	git clone url

4.操作

​	git status	查看文件跟踪状态

​	git add . 添加文件到暂存区，待提交到本地仓库

​	git commit -m “name”提交暂存区文件到本地仓库，m代表提交信息

https://gitee.com/MrPrefect-chb/git.git

5.gitee生成密钥

​	ssh-keygen -t rsa

6.克隆gitee仓库

​     git clone https://gitee.com/MrPrefect-chb/git.git

7.IDEA集成Git

​	将生成的克隆远程仓库文件放到项目目录

8.Git分支

​	git branch列出所有本地分支

​	git branch -r列出所有远程分支

​	git branch [branch-name]新建一个分支，但是依旧停留在当前分支

​	git checkout -b [branch]新建一个分支，并切换到该分支

​	git merge [branch]合并指定分支到当前分支

​	git branch -d [branch-name]删除分支

删除远程分支

​	git push origin --delete [branch-name]

​	git branch -dr [remote/branch]

新建远程分支

http://chenhebin@10.1.51.186/NET-APPS/demo/ktna-demo-project.git

http://10.1.51.186/NET-APPS/demo/ktna-demo-project/tree/feat/@chenhebin/dev

git@10.1.51.186:NET-APPS/demo/ktna-demo-project/tree/feat/@chenhebin/dev.git

