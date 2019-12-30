---
layout: post
title: VS2019手动搭建自己的nuget服务器及使用
category: 技术
tags: Git
keywords: nuget
description:
---

> Nuget是一个.NET平台下的开源的项目，它是Visual Studio的扩展。在使用Visual Studio开发基于.NET Framework的应用时，Nuget能把在项目中添加、移除和更新引用的工作变得更加快捷方便。

## 1.搭建自己的私有的nuget服务器

首先打开VS2019,点击创建新项目
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/1.png)

找到ASP.NET WEB 应用程序（.NET Framwork）模板
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/2.png)

如果找不到该模板，可以滚动到模板最下面得"安装多个工具和功能"，选择"ASP.NET和Web开发"，在右侧选中"其它项目模板（早期版本）"
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/3.png)

选则得框架大于4.6即可，点击创建
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/4.png)

选中创建空模板
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/5.png)

右键选择管理NuGet包，搜索"NuGet.Server"并安装
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/6.png)

安装完后，修改Web.config，删除  <compilation debug="true" targetFramework="4.6" />节点，然后运行项目
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/7.png)

目前为止，搭建nuget服务器完成。后续如何添加到IIS我就不详细说明，毕竟微软是傻瓜式发布。

## 2.打包代码为nuget包
### 方法1：
使用VS2019自带的工具，右键--打包，复制后缀.nupkg文件到服务器站点的Packages目录下即可
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/8.png)

### 方法2：
[使用NuGet Package Explorer发布](https://www.cnblogs.com/xieyang07/p/10193283.html)

## 3.在其他项目中使用私有服务器上的nuget包
打开项目，把本地nuget私有服务器地址添加
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/9.png)

搜索名称即可
![github-flow](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Manually-build-your-own-nuget-server/10.png)