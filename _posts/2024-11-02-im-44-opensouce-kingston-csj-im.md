---
layout: post
title: IM 即时通讯系统-44-仿QQ即时通讯系统服务端
date: 2024-11-02 21:01:55 +0800
categories: [IM]
tags: [im, opensource, sh]
published: true
---


# IM

https://github.com/kingston-csj/im

## im(仿QQ聊天室服务端)
### 主要使用的第三方技术  
*  Netty，通信网关  
*  spring，作为IOC容器  
*  MyBatics，作为持久化方案  

### 功能列表  
*  用户注册/登录/好友列表/私聊界面(包括用户界面)  
*  通过http后台请求管理服务进程  
*  用户数据的持久化  
*  使用独立线程池处理用户消息 　

### ToDoList  
*  使用SpringCloud打造为分布式系统   
*  在线/离线文件传输   
*  异步处理用户数据的持久化  
*  开发更多的交互功能        

### QuickStart  
1. 安装git后，使用命令 git clone https://github.com/kingston-csj/im 
2. 新建数据库im，导入im-chat/src/test/resources目录下的chat_room.sql   
3. 在applicationContext.xml文件配置本地数据库连接属性，启动ServerStartup
4. 另起新目录，下载客户端代码 git clone https://github.com/kingston-csj/wechat
5. 启动ClientStartup类, 即可看到登录界面（临时密码为000）  
   (多人聊天，需要开启多个ClientStartup客户端即可)

### 部分客户端运行效果

登录界面  

![登录界面](https://github.com/kingston-csj/im/raw/master/screenshots/login.png)  

主界面  

![主界面](https://github.com/kingston-csj/im/raw/master/screenshots/main.png)  
　　
### 案例教程 

栏目教程 --> [csdn专栏博客](https://blog.csdn.net/littleschemer/article/category/9269527)  

wiki说明 --> [wiki](https://github.com/kingston-csj/im/wiki)
 
### 客户端源代码  

--> [客户端wechat](https://github.com/kingston-csj/wechat)

# 参考资料

* any list
{:toc}