---
layout:     post
title:      "解决Github上传下载慢的问题"
subtitle:   "不负责的"
date:       2018-05-07 01:00:00
author:     Dennis
header-img: ""
tags:
    - Github
    - 实用技巧
---

> 前言：
> 想整理打包下以往的代码，上传了半天没成功，还报了"Failed to publish this branch"，emmm，查了一下，一通乱试，好像OK了？

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
