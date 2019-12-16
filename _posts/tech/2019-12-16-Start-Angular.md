---
layout: post
title: 开始一个Angular项目
category: 技术
tags: [Angular]
keywords: Angular
---

# StartAngular
创建一个默认模板的Angular项目

> 开发工具推荐：[VS Code](https://code.visualstudio.com)  
> 推荐安装插件：[NG-ZORRO](https://marketplace.visualstudio.com/items?itemName=cipchk.ng-zorro-vscode)、
> [Beautify css/sass/scss/less](https://marketplace.visualstudio.com/items?itemName=michelemelluso.code-beautifier)、
> [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)、
> [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)、
> [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

## 链接

- [Angular Document](https://www.angular.cn/guide/quickstart)
- [NG-ZORRO Document](https://ng.ant.design/docs/introduce/zh)

## 环境准备

### 1. 安装 [Node.js](https://nodejs.org/en/download/)。

运行命令： `node -v` 查看版本，要求 10.x 以上。  
运行命令： `npm -v` 查看版本，要求 6.x 以上。

### 2. 设置淘宝的镜像

运行命令：`npm config set registry https://registry.npm.taobao.org` ，_如需还原，运行命令：`npm config set registry https://registry.npmjs.org`_。

### 3. 安装全局 `Angular cli`

运行命令：npm install -g @angular/cli，_如需卸载，运行命令：`npm uninstall -g @angular/cli`_。

### 4. 安装 node sass 所需构建环境 

配置 `SASS_BINARY_SITE` 的地址，运行命令：`npm config set SASS_BINARY_SITE https://npm.taobao.org/mirrors/node-sass`

## 创建新项目

在终端中，进入想要创建项目的地址执行 :ng new 项目名称

执行完该命令后会下载相应的npm包，耐心等待

注意：执行ng new yourapp后会有一段提示说【'git' 不是内部或外部命令，也不是可运行的程序或批处理文件。】，这个和本地没有安装git有关，但是不会影响项目。

## 启动项目

在终端中进入项目所在文件夹:cd yourapp;

运行命令：`ng serve` (默认为 dev 环境)， 浏览器导航到：`http://localhost:4200/`，如果您更改任文件，应用程序将自动新加重载。

## 项目结构说明

![cover](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Start-Angular/20191216155337.png.jpg)

## 代码脚手架

**运行 `ng generate component component-name` 生成新组件。**

> 您还可以使用`ng generate directive | pipe | service | class | guard | interface | enum | module`。

**可选参数如下：**

> `--spec=false` 不生产单元测试文件  
> `--module=module-name` 加入到指定的模块

## 构建

运行 `ng build` 来构建项目。 构建工件将存储在 `dist/` 目录中。 使用 `--prod` 标志进行生产构建。

## 运行代码检查

运行 `ng lint` 来检查代码规范。

## 运行单元测试

运行 `ng test` 以通过 [Karma](https://karma-runner.github.io) 执行单元测试。

## 运行端到端测试

运行 `ng e2e` 以通过 [Protractor](http://www.protractortest.org/) 执行端到端测试。

## 更多帮助

要获得 Angular CLI 的更多帮助，请使用 `ng help` 或查看 [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md) 。

## 环境配置常见错误及解决方案

##### 1. 发生错误：gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.

> 需要安装 [python 2.x](https://www.python.org/downloads/) 版本

##### 2. 发的工具生错误：MSBUILD : error MSB4132: 无法识别工具版本“2.0”。可用版本为 “4.0”

> 打开【控制面板】——【程序】——【启用或关闭 windows 功能】—— 勾选低版本的 .net

##### 3. MSBUILD : error MSB3428: 未能加载 Visual C++ 组件"VCBuild.exe"

> 需要通过命令行安装：`npm install -g windows-build-tools`
