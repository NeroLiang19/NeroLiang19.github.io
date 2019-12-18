---
layout: post
title: 从一台电脑上传文件到Github上
category: 技术
tags: Github
description: 转载学习
---

> 目标：从一台电脑上传文件到Github上

## 前提：

 1、这里假定已在Github上创建了仓库，建立了仓库

 2、已在这台电脑上安装了Git客户端

## 实验环境：

 1、Windows 10 64位，已安装了Git for Windows的客户端

## 重点说明：

 1、在本机初始化和配置Git客户端

 2、要从某台电脑上上传文件到GitHub，需要把在本机生成的密钥配置到GitHub上

## 步骤：

 1.选择工作文件夹，点鼠标右键，点“Git Bash Here”
	
![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/1.png)

## 2、初始化

	git init

## 3、配置你在Git hub 上创建账号使用的：user name和user  email

 git config --global user.name 'user name'

 git config --global user.email 'your email account'
	
 用你在Git hub上实际的user name替换'user name'，你的邮箱账号xxx@xxx.com替换'your email account'

## 4.创建密钥

 ssh-keygen -t rsa -C "your_email@example.com"

 Windows生成密钥的位置：c:\users\username\.ssh

## 5.拷贝密钥

 windows下：切换到 c:\users\username\.ssh  ls

 会看到如下文件：
	
![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/2.png)

 用文本编辑器打开id_rsa.pub，拷贝里面的内容或者:cat id_rsa.pub 显示里面的密钥内容后，拷贝

## 6.从web登录github.com

## 7.点右上角,向下的三角符合，点选Settings
	
![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/3.png)

## 8.点左侧的 SSH and GPG Keys

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/4.png)

## 9.点New SSH key按钮

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/5.png)

## 10.在下图Titile栏，可自己填写取的名字，Key栏，把前面从id_rsa.pub里复制的密钥字符串拷贝进去，再点Add new SSH按钮

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/6.png)

## 11.测试是否能顺利连通GitHub

 ssh -T git@github.com

 出现下面情况，表明已经能连到Github了

 username!You've successfully authenticated,but GitHub does not provide shell access

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Connect-github-with-your-PC/7.png)

## 12.向GitHub推送文件

## (1)、添加，采用下面几种方式添加

 git add
 git add fileName
 git add folderName

## (2)、提交

 git commit -m 'commit remark'

## (3)、推送到GitHub

 方式1,推送到master主分支 git push oringin master
 方式2，推送到某个分支 git push oringin branchName

## (4)、登录到GitHub上检查是否推送成功。
	  
转载自:[Git学习笔记——从一台电脑上传文件到Github上](https://www.cnblogs.com/SH170706/p/10570598.html)
