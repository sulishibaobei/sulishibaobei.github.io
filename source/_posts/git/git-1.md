---
title :  git 打卡的第一天 
date : 2017-09-07 9:08:53
categories: "Git"
tag:
 - git   
 - 环境变量配置
 - 基本git操作
---
因为某种原因，所以不得不重新巩固下前端的基础知识，从最基本的学习还得额外的学习新知识，倍感压力之大。 昨天初略学习下git，算是自己学习的一个新知识。简单记录下，希望四海八荒的大神看过来，有错的请指导。
## git下载安装 
[git下载安装地址](https://git-for-windows.github.io/)下载好之后记得配置环境变量。  
**git的作用主要是将项目保存到git目录中** 
<!--more-->
## git介绍
**主要分为三个块：** 
[1] working directory(工作目录）  本地上存放项目的目录。
[2] staging area 暂存放区域    更新暂未提交的项目。
[3] git directory  git上存放项目的地方。
1.一般我们使用git都会将一些修改的文件保存在暂存区域，然后一并提交更新到git目录中。 
2.安装好git之后，网页打开github，注册信息。登陆进去，接下来配置信息。 
3.打开右上角，如下图点击settings。然后点击SSH and GPG keys ,配置SSH。打开cmd或者git bash。找不到git.bash的就在开始那里搜索或者在你安装的git文件里面打开git.bash.exe就一样。 
4.在命令行输入：<font color=red>ssh-keygen -t rsa -C "你注册github时的邮箱名" </font>然后会提示你生产的key在id_rsa.pub文件中找。
![git](/images/git-1.png)
5.在电脑中一个叫.ssh的文件里，然后复制黏贴在右边的key框里面，title就随便打。 
![git](/images/git-2.png)
![git](/images/git-3.png)
 此时git的安装目录就为你的根目录。
 ## 配置用户信息： 
`git config --global user.name "你注册时的用户名"; `
`git config  --global user.email "你的邮箱";`
git里面提供了一个git config的命令专门用来配置或者读取相应的工作环境变量。 
这些变量可以存放在三个地方：etc/gitconfig文件， git config --system读写的就是此文件；~/.gitconfig文件，git config --global 读写的就是此文件； 
配置好信息后，可以通过git config --list 命令查看&nbsp;&nbsp; 如果git config user.name就是具体的查看某个环境变量的设定。 
**若不知道如何使用git命令，可以git help看看。** 
## git使用。 
![git](/images/git-4.png)
 新建一个git 仓库，也可以导入一个本地项目。进入仓库 
 复制这个地址 
![git](/images/git-5.png)
 然后打开cmd或者git.bash.exe， 
 输入`git clone git@github.com:sulishibaobei/a.github.io.git` （上面复制的这串地址) 
 就会在你的本地建立一个名为a.github.io的仓库，进入仓库，将你需要上传的代码拷贝进去 

```javascript
git add *（*是上传所有的本件，若是想具体的就写文件名）
git commit  -m "version"  提交文件
git push 
```
 这三步就完成git项目的提交。 
 可以用git status命令查看当前文件的状态。 
 所有的文件都可以先add到暂存区域，然后最后再一起commit。 
  
 