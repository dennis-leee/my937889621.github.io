---
layout:     post
title:      "Unity3D+EasyAR实例学习（一）"
subtitle:   "HelloAR实例"
date:       2018-04-14 19:00:00
author:     Dennis
header-img: ""
tags:
    - 学习
    - EasyAR
    - Unity
---

> ***前言***
> *因为网上有很多这种教程，所以这里可能会对主要步骤讲的简略一点，着重讲一下博主自己配置过程中遇到的问题和解决方法*

# 一、环境部署
## 1. Unity3D
直接去[官网](https://unity3d.com/cn)下载安装即可，博主使用的是2017.4.1最新版本。记得安装的时候勾选相应的发布平台（Android、IOS）支持，默认的是没勾的，这里博主用的是安卓.（网上找的图，别在意版本号）  
![installUnity](\img\in-post\Unity3DXEasyAR\Unity安装.png)  
至于VS，已经装有的了话记得取消勾选。

## 2. EasyAR
1. 去[官网](https://www.easyar.cn/view/download.html)下载SDK和Samples：(Basic版即可)  
![EasyARSDK](\img\in-post\Unity3DXEasyAR\EasyARSDK.png)  
![EasyARSamples](\img\in-post\Unity3DXEasyAR\EasyARSamples.png)  
将其Samples解压到**无中文，无空格的目录** **无中文，无空格的目录** **无中文，无空格的目录**下，SDK可以先不管。  
![unzipSamples](\img\in-post\Unity3DXEasyAR\unzipSamples.png)

2. 申请Key  
注册账号就不多说了，登录后进入个人界面 → SDK授权管理 → 添加SDK license key  
![addKey](\img\in-post\Unity3DXEasyAR\addKey.png)  
![addKey1](\img\in-post\Unity3DXEasyAR\addKey1.png)  
应用名称随便填，Bundle ID（IOS）和PackageName（Android）在发布应用时会用到，按照如下格式填写：  
`com.XXX.XXX`
中间为组织名，后面为应用名。

## 3. JDK && SDK(Android)  
因为博主没有IOS，所以这里以Android为例，详细安装和配置教程可以参考  
[unity安卓发布的环境配置和基本流程](https://blog.csdn.net/qq_22660469/article/details/78922367)
IOS可以去网上查查相应的配置教程。

# 二、调试运行
1. 启动项目  
用Unity打开Samples中的HelloAR项目文件  
2. 打开场景  
在Project视图中选择`Assets → HelloAR → Scenes → HelloAR`  
![Open](\img\in-post\Unity3DXEasyAR\Open.png)  
3. 修改Key值  
在Hierachy视图中选择'HelloAR→EasyAR_Startup'，在Inspector视图中修改Key值为**之前申请的Key值**  
![addKey2](\img\in-post\Unity3DXEasyAR\addKey2.png)  
4. 运行  
到这一步PC有摄像头的话应该是可以正常运行了。如果没有摄像头，效果应该是黑屏，如果有其他提示信息，请检查之前的步骤。  
5. 发布到Andriod平台  
 - 选择`Edit → preferences → External Tools`，将SDK和JDK设为之前安装的位置  
 ![SDKJDK](\img\in-post\Unity3DXEasyAR\SDKJDK.png)  
 - 选择`File → Build Setting`，选中Android后，点击`Switch Platfrom`，待其加载完毕后，点击旁边的`Plarer Setting`，查看Unity主界面的Inspector视图，修改`Others Settings → Identification → Package Name`为**之前申请Key的时候设置的PackageName**。  
 ![name](\img\in-post\Unity3DXEasyAR\name.png)  
 然后就可以Build了。  

# 三、效果图
![result](\img\in-post\Unity3DXEasyAR\result.png)

OK。


　






