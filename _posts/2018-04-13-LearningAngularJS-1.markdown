---
layout:     post
title:      "AngularJS快速上手（一）"
subtitle:   "Angular CLI的安装及基本使用"
date:       2018-04-13 02:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - 学习
    - 系统分析与设计
    - AngularJS
    - Homework
---

> ***前言***
> *因为课程大作业需要用到AngularJS，故此记录学习兼使用过程*

# 一、简介
## 1. 什么是AngularJS？
> *[AngularJS](https://angular.cn/)是一个开发动态Web应用的框架。它让你可以使用HTML作为模板语言并且可以通过扩展的HTML语法来使应用组件更加清晰和简洁。它的创新之处在于，通过数据绑定和依赖注入减少了大量代码，而这些都在浏览器端通过JavaScript实现，能够和任何服务器端技术完美结合。*

## 2.什么是Angular CLI？
> [Angular CLI](https://cli.angular.io/) 是一个命令行接口，用于实现Angular工程的自动化部署、开发、测试和发布。

# 二、安装
## 1. 安装[nodeJS](https://nodejs.org/en/)和npm
在使用 Angular CLI 之前，你必须确保系统中 Node.js 的版本高于 6.9.0 且 npm 的版本高于 3.0.0。
若已安装，可用如下命令查看环境信息：
```
$ node -v #查看nodeJS版本号
$ npm -v  #查看npm版本号
```
若未安装，可直接去[官网](https://nodejs.org/en/)下载安装
![node.js](/img/in-post/LearningAngularJS-1/nodejs.png)
具体安装与配置步骤可以参考[node和npm的安装和镜像源的修改](https://www.cnblogs.com/ryze/p/ryze11.html)

这里我之前就已经安装好了
![check nodejs version](/img/in-post/LearningAngularJS-1/checkVersion.png)
不过出于强迫症，还是升级一下版本吧，Windows直接去官网下载安装包覆盖安装，Linux可以使用如下指令更新：
```
$ npm update -g    #更新npm
$ npm cache clean  #清除npm缓存
$ npm install -g n  #下载n模块（该模块专门用来管理node.js的版本）
$ n stable  #升级到最新稳定版本
```
更新成功：
![update node js](/img/in-post/LearningAngularJS-1/update.png)
## 2. 安装Angular CLI
```
$ npm install -g @angular/cli   #安装
$ ng version                    #验证安装
```
若无误，应出现以下界面
![install CLI](/img/in-post/LearningAngularJS-1/installCLI.png)
![vertificate CLI](/img/in-post/LearningAngularJS-1/vertificateCLI.png)

# 三、创建新项目
开启终端窗口，进入项目目录，输入如下指令
```
$ npm new my-app    # 在当前目录新建项目my-app
```
该命令会为我们生成一个AngularJS项目的标准框架，需要等待较长时间。

（开个坑，回头再分析生成的项目文件）
```
E:\我的文档\AngularJS Projects>ng new my-app
  create my-app/e2e/app.e2e-spec.ts (288 bytes)
  create my-app/e2e/app.po.ts (208 bytes)
  create my-app/e2e/tsconfig.e2e.json (235 bytes)
  create my-app/karma.conf.js (923 bytes)
  create my-app/package.json (1291 bytes)
  create my-app/protractor.conf.js (722 bytes)
  create my-app/README.md (1021 bytes)
  create my-app/tsconfig.json (363 bytes)
  create my-app/tslint.json (3012 bytes)
  create my-app/.angular-cli.json (1241 bytes)
  create my-app/.editorconfig (245 bytes)
  create my-app/.gitignore (544 bytes)
  create my-app/src/assets/.gitkeep (0 bytes)
  create my-app/src/environments/environment.prod.ts (51 bytes)
  create my-app/src/environments/environment.ts (387 bytes)
  create my-app/src/favicon.ico (5430 bytes)
  create my-app/src/index.html (292 bytes)
  create my-app/src/main.ts (370 bytes)
  create my-app/src/polyfills.ts (3114 bytes)
  create my-app/src/styles.css (80 bytes)
  create my-app/src/test.ts (642 bytes)
  create my-app/src/tsconfig.app.json (211 bytes)
  create my-app/src/tsconfig.spec.json (283 bytes)
  create my-app/src/typings.d.ts (104 bytes)
  create my-app/src/app/app.module.ts (316 bytes)
  create my-app/src/app/app.component.html (1141 bytes)
  create my-app/src/app/app.component.spec.ts (986 bytes)
  create my-app/src/app/app.component.ts (207 bytes)
  create my-app/src/app/app.component.css (0 bytes)
npm WARN deprecated nodemailer@2.7.2: All versions below 4.0.1 of Nodemailer are deprecated. See https://nodemailer.com/status/

npm WARN deprecated mailcomposer@4.0.1: This project is unmaintained
npm WARN deprecated socks@1.1.9: If using 2.x branch, please upgrade to at least 2.1.6 to avoid a serious bug with socket data
flow and an import issue introduced in 2.1.0
npm WARN deprecated node-uuid@1.4.8: Use uuid module instead
npm WARN deprecated buildmail@4.0.1: This project is unmaintained
npm WARN deprecated socks@1.1.10: If using 2.x branch, please upgrade to at least 2.1.6 to avoid a serious bug with socket data
 flow and an import issue introduced in 2.1.0

> uws@9.14.0 install E:\我的文档\AngularJS Projects\my-app\node_modules\uws
> node-gyp rebuild > build_log.txt 2>&1 || exit 0


> node-sass@4.8.3 install E:\我的文档\AngularJS Projects\my-app\node_modules\node-sass
> node scripts/install.js

Cached binary found at C:\Users\gesangdianzi\AppData\Roaming\npm-cache\node-sass\4.8.3\win32-x64-57_binding.node

> uglifyjs-webpack-plugin@0.4.6 postinstall E:\我的文档\AngularJS Projects\my-app\node_modules\webpack\node_modules\uglifyjs-we
bpack-plugin
> node lib/post_install.js


> node-sass@4.8.3 postinstall E:\我的文档\AngularJS Projects\my-app\node_modules\node-sass
> node scripts/build.js

Binary found at E:\我的文档\AngularJS Projects\my-app\node_modules\node-sass\vendor\win32-x64-57\binding.node
Testing binary
Binary is fine
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.1.3 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.3: wanted {"os":"darwin","arch":"any"} (cur
rent: {"os":"win32","arch":"x64"})

added 1259 packages from 1241 contributors in 261.27s
Project 'my-app' successfully created.
```

# 四、启动开发服务器
终端进入项目目录，输入如下指令：
```
ng serve --open
```
`ng serve`指令启动开发服务器，并监听文件变化，在文件被修改时重构该应用。
`--open`参数自动打开浏览器，并访问`localhost:4200`
![serve open](/img/in-post/LearningAngularJS-1/serveOpen.png)
![welcome page](/img/in-post/LearningAngularJS-1/welcomePage.png)

# 五、编辑第一个Angular组件
CLI已经为我们创建了第一个Angular组件：`app-root`。
可以在`./src/app/app/component.ts`目录下找到它。
打开该文件
`component.ts`
```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'My First Angular App';
}

```
修改`title`属性为指定内容保存，如
> *My First Angular App*

CLI自动重构该应用：
![app-root](/img/in-post/LearningAngularJS-1/app-root.png)

至此，初步使用已OK。
