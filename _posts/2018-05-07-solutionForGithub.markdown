---
layout:     post
title:      "Github相关问题"
subtitle:   "不负责的"
date:       2018-05-07 13:00:00
author:     Dennis
header-img: ""
tags:
    - Github
    - 实用技巧
---

> 前言：
> 想整理打包下以往的代码，上传了半天没成功，还报了"Failed to publish this branch"，emmm，查了一下，一通乱试，好像OK了？

## 一、上传下载慢

### 方法一：翻墙
 不多说。

### 方法二：改host
国内Github的DNS污染严重，故绕过DNS，手动解析IP。
1. 打开[DNS查询网站](http://tool.chinaz.com/dns)  
分别查询github.global.ssl.fastly.net、assets-cdn.github.com、github.com，选择一个TTL较小的，复制IP，放到HOST里面即可。
![check ip](/img/in-post/solutionForGithub/查dns-ip.png)
![host](/img/in-post/solutionForGithub/host.png)

### 方法三：用ssh
如果方法二无效，可以试试这个（嗯，同样不保证有效）。貌似Git的默认push方式是https，这种方式受到上面原因的影响，速度慢的不行，我这只有60-80K/s，改了之后有500+K/s，好像还行。
1. 打开Git Shell，执行以下命令：  
`git config --global url.ssh://git@github.com/.insteadOf https://github.com/`
2. 重启客户端，OK。

## 二、Failed to publish/sync this branch
（注：个人向解决方案）  
照着下面的试着改成ssh，发现速度暴增，publish成功，刚高兴没多久，sync又失败了...  
[Github错误：Failed to publish this branch](https://www.cnblogs.com/shoneworn/p/5262418.html)  
继续查：  
[GitHub for Windows提交失败“failed to sync this branch”](https://blog.csdn.net/gzu_cs_yige23/article/details/50722454)  
照着弄，新的问题又出现了,emmmm
`Permission denied (publickey).`
继续查，追到了[逼乎](https://www.zhihu.com/question/21402411)
原来是我以前建密钥的时候用了自定义的名称（github_rsa），需要在.ssh/目录下添加个config文件：  
```
Host github.com
 HostName github.com
 User git
 IdentityFile ~/.ssh/github_rsa
 ```
这下总能git pull了，然后git push又出问题了,emmmm  
不过shell还是好debug啊，仔细看看，发现是有个文件太大了，超过了github单文件限制(100MB)，删除后，重试，还是卡住了，WTK？！  
猜测是因为版本记录的问题，已经把大文件写进记录里了。干脆直接重建仓库吧！
1. 在客户端移除本地仓库
2. 删除仓库下的.git文件夹。
3. 新建仓库，Commit, publish, sync, ok（逃
