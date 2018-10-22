---
layout:     post
title:      "系统分析与设计 Homework - 6"
subtitle:   "状态建模"
date:       2018-05-05 18:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - 学习
    - 系统分析与设计
    - Homework
---

建模工具：UMLet 14.2

### 1. 使用UML State Model
 - 建模对象：参考Asg_RH文档，对Reservation/Order对象建模。
 - 建模要求：参考现在定旅馆的旅游网站，尽可能分析围绕订单发生的各种情况，直到订单通过销售事件（柜台销售）结束订单。
 - 状态集合S={待填写，待检查，待支付，待审核， 归档}
 - 事件集合={旅客申请订单，旅客填写并提交订单，订单信息有误，旅客取消订单，旅客确认订单，订单超时未支付，旅客重新申请订单，旅客支付订单，后台审核通过，后台审核不通过，系统退款，旅客退出，旅客入住，旅客退房，旅客评价或不评价订单}

 ![State Model](/img/in-post/SAAD-homework/hw6/订旅馆.png)

### 2. 研究[淘宝退货流程活动图](http://bigdata.taobao.com/doc.htm?spm=a219a.7629065.1.21.mjYP25#?docType=1&docId=102594)，对退货业务对象状态建模

![State Model](/img/in-post/SAAD-homework/hw6/淘宝退货.png)
