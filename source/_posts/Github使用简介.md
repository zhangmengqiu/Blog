title: Git使用简介
author: Tao Feng
tags:
  - Useful Tools
  - Github Project
categories: []
date: 2016-12-04 23:32:00
---
### Github是什么
作为一名程序猿还是一名程序媛，[Git](https://github.com)肯定是你不可或缺的版本控制工具（VCS），即便可能你不想用，但是好多开源的代码都放在Github或自建的git服务器上，你要下载下来可能就需要用到它了。Github则是全球程序员和开源项目的聚集地。Git作为版本控制的工具主要有一下几个好处。

第一，方便分工合作。一个大型的软件肯定是要大量的人力累积出来的，一个人的力量很有限，没有合作就没有现在软件的发展。那么如何使得所有人都有一个所有代码的副本，同时可以获得最新的代码和提交最新的代码呢？而且代码一旦多起来，More is Different！那么真的连“亲妈”都不认识。所以每个人都只需要专注于自己的那一个模块。

第二，方便管理。有了Git之后，即便有人不小心误删了某些代码，还能回滚回去。即便有机器Down掉了也不会有灾难性的影响。另外，Git可以将不同的开发组分到不同的Branch进行开发，每个组只有相应Branch的提交权限，这样管理更有效。

第三，方便开发。Git将项目分为不同的Branch，将新特性放在单独的一个Branch下开发，当功能测试稳定后再Merge到主分支，这样就是一个迭代的开发流程，提高开发效率。

扯了这么多，来点干货。

### Git基本命令

初建一个工程，$PROJECTS_ROOT为你所有项目存放的目录，代表一个变量
```bash
cd $PROJECTS_ROOT
mkdir project_name
cd project_name
git init
```
git init这个命令其实在项目下新建了一个.git目录，目录下有Git的配置和存档信息。另外可以直接从Git服务器上将一个开源项目clone下来
```bash
cd $PROJECTS_ROOT
git clone https://github.com/osgee/c9ssh.git
```
这里注意，结尾必须是[.git]，不然虽然可以clone下来项目，但是当提交是会报错，得修改.git/config文件中的[remote "origin"]项下的url值，也可以通过git remote set-url origin 修改。git clone曲折的做法可以是
```bash
cd $PROJECTS_ROOT
mkdir c9ssh
cd c9ssh
git init
git remote add origin https://github.com/osgee/c9ssh.git
git pull origin master
```
这里的git remote add origin是添加源Git仓库的地址，origin是该源的名称，可以自己改。不过只有一个源仓库时，公认的命名方法是origin，但要添加多个源时，每个就需要有单独的名称。git pull origin master的意思是从叫origin的源仓库中提取master分支的数据。

怎么进行第一个提交呢？需要认识到Git将文件划分的几个状态，以及不同的命令对应得文件状态的改变
![git file status](/assets/blogImg/git_status.png)
下面通过命令进行第一次提交，假设你在Github上已经注册的用户名为[username]，新建了一个项目名为[project_name]，那么根据Github的命名规则，这个项目的git仓库的地址为https://github.com/username/project_name.git . 在电脑上的命令如下
```bash
cd $PROJECTS_ROOT
git clone https://github.com/username/project_name.git
cd project_name
echo "### first git project">>README.md
git add README.md
git commit -m "first commit"
git push origin master
```

下面是高级一点的命令，包括搭建本地的git仓库，对远端仓库的修改等。这也是我github上分享的一个项目，翻译不过来，将就着看吧

### Git

#### server

using project to init repo

	git clone --bare project project.git

create bare repo
	
	mkdir project.git && cd project.git && git --bare init 

or

	git init --bare project.git

share with group

	git init --bare --share project.git
	
### clone 

git clone remote repo <c9ssh> to local repo <ssh>
	
	git clone https://github.com/osgee/c9ssh.git ssh

git clone a bare repo

	git clone --bare https://github.com/osgee/c9ssh.git

copy <project> to remote repo

	scp -r project.git git@git.osgee.com:~/repos/

### remote

show remote repos details

	git remote -v

show status of remote repo <origin>

	git remote show origin

add remote repo <c9ssh>

	git remote add origin git@github.com:osgee/c9ssh.git
	git remote add origin https://github.com/osgee/c9ssh.git

revise remote repo to <ssh>
	
	git remote set-url origin git@github.com/ssh.git

remove reomte repo <origin>

	git remote rm <orign>

rename remote repo <origin> to <fork>

	git remote rename origin fork

remote add branchs using namespace <qa> 

	git remote set-branchs --add origin <qa>/*

set remote repos' HEAD to master

	git remote set-head origin master

### checkout

switch branch to <master>

	git checkout <master>

add branch <newbranch> and swich to it

	git checkout -b <newbranch>

add branch <newbranch> based on <branch> and switch to it

	git checkout -b <newbranch> <branch>

track remote branch <branch> on repos <origin> and create a local one

	git checkout --track <origin>/<branch>

### branch

show present branches

	git branch

show merged branch

	git branch --merged

show not merged branch

	git branch --no-merged

track remote repo origin/master

	git branch -u master [origin/master]

equal to

	git branch --set-upstream master origin/master

delete branch

	git branch -d <branch>

delete branch forcely

	git branch -D <branch>


### pull fetch merge

fetch from remote repo and merge to local repo 

	git pull origin master
	
equals
	git fetch origin master
	git merge origin/master

no Fast-Forward

	git merge origin/master --no-ff

rebase master to <branch>

	git rebase master <branch>

equals

	git checkout <branch> && git rebase master && git checkout master && git merge <branch>

### push

push all branches

	git push

push master to branch master on remote repos origin

	git push origin master

if not inited in remote repo

	git push -u origin master

add remote repo a new branch

	git push origin <branch>

or

	git push orign <local_branch>:<remote_branch>

delete remote branch

	git push origin :<branch>

may you need to remove local branch in advance

	git branch -d <branch>

### stash

	git stash

	git stash list

	git stash apply

	git stash drop

### log

	git log

	git log --decorate --graph --oneline --all

### reset

	git reset --hard commit-sha
	git reset --hard HEAD~
	git reset <file>	

### add

	git add .
	git add -A
	git add *
	git add <file>

### rm

	git rm <file>

### diff

	git diff <file>
	git diff --staged
	git diff --cached