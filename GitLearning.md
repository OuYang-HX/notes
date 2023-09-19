# Git learning
## Git 安装
## Git基本命令
1.`git status <filename>`:查看本地分支增删状态
2.`git add <filename>/.`:将增加的文件或修改的文件提交到本地分支
3.`git rm <filename>`：将删除的文件从本地分支删除
4.`git commit <filename>/-a -m "commit info"`:提交到本地分支
5.`git commit --amend`:修改commit内容和信息
6.`git log `:查看节点信息
- `[-1]`:查看最近一个节点的信息
- `[--name-status]`:查看增删改信息

7.`git checkout <barnchname>`:切换分支
- `-b`:新建并切换分支

8.`git push origin master`:推送到服务器origin的master分支上
- `[-f]`:强制推送
9.`git remote`:查询所连接的远端服务器名称
10.`git merge branchname1 branchname2`:把分支1合并到分支2，分支节点id不变
11.`git cherry-pick nodeID`:将其他分支节点合并到所在分支,只同步内容,生成新ID
12.`git reset --hard nodeID`:回退到某个节点
13.`git checkout filename/.`:工作区未提交文件(.全部文件)的回退
## Git提交推送
