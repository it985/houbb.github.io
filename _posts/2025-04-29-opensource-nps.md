---
layout: post
title: NPS 一款轻量级、高性能、功能强大的内网穿透代理服务器
date: 2025-4-29 09:10:20 +0800
categories: [Tool]
tags: [tool, net, opensource, sh]
published: true
---



# 是什么

一款轻量级、高性能、功能强大的内网穿透代理服务器。

支持tcp、udp、socks5、http等几乎所有流量转发，可用来访问内网网站、本地支付接口调试、ssh访问、远程桌面，内网dns解析、内网socks5代理等等……，并带有功能强大的web管理端。

a lightweight, high-performance, powerful intranet penetration proxy server, with a powerful web management terminal.

# nps

nps是一款轻量级、高性能、功能强大的**内网穿透**代理服务器。

目前支持**tcp、udp流量转发**，可支持任何**tcp、udp**上层协议（访问内网网站、本地支付接口调试、ssh访问、远程桌面，内网dns解析等等……），此外还**支持内网http代理、内网socks5代理**、**p2p等**，并带有功能强大的web管理端。


## 背景

![image](https://github.com/ehang-io/nps/blob/master/image/web.png?raw=true)

1. 做微信公众号开发、小程序开发等----> 域名代理模式

2. 想在外网通过ssh连接内网的机器，做云服务器到内网服务器端口的映射，----> tcp代理模式

3. 在非内网环境下使用内网dns，或者需要通过udp访问内网机器等----> udp代理模式

4. 在外网使用HTTP代理访问内网站点----> http代理模式

5. 搭建一个内网穿透ss，在外网如同使用内网vpn一样访问内网资源或者设备----> socks5代理模式

## 特点

- 协议支持全面，兼容几乎所有常用协议，例如tcp、udp、http(s)、socks5、p2p、http代理...
- 全平台兼容(linux、windows、macos、群辉等)，支持一键安装为系统服务
- 控制全面，同时支持服务端和客户端控制
- https集成，支持将后端代理和web服务转成https，同时支持多证书
- 操作简单，只需简单的配置即可在web ui上完成其余操作
- 展示信息全面，流量、系统信息、即时带宽、客户端版本等
- 扩展功能强大，该有的都有了（缓存、压缩、加密、流量限制、带宽限制、端口复用等等）
- 域名解析具备自定义header、404页面配置、host修改、站点保护、URL路由、泛解析等功能
- 服务端支持多用户和用户注册功能

**没找到你想要的功能？不要紧，点击[进入文档](https://ehang-io.github.io/nps)查找吧**
## 快速开始

### 安装
> [releases](https://github.com/ehang-io/nps/releases)

下载对应的系统版本即可，服务端和客户端是单独的

### 服务端启动
下载完服务器压缩包后，解压，然后进入解压后的文件夹

- 执行安装命令

对于linux|darwin ```sudo ./nps install```

对于windows，管理员身份运行cmd，进入安装目录 ```nps.exe install```

- 默认端口

nps默认配置文件使用了80，443，8080，8024端口

80与443端口为域名解析模式默认端口

8080为web管理访问端口

8024为网桥端口，用于客户端与服务器通信

- 启动

对于linux|darwin ```sudo nps start```

对于windows，管理员身份运行cmd，进入程序目录 ```nps.exe start```

```安装后windows配置文件位于 C:\Program Files\nps，linux和darwin位于/etc/nps```

**如果发现没有启动成功，可以查看日志(Windows日志文件位于当前运行目录下，linux和darwin位于/var/log/nps.log)**
- 访问服务端ip:web服务端口（默认为8080）
- 使用用户名和密码登陆（默认admin/123，正式使用一定要更改）
- 创建客户端

### 客户端连接
- 点击web管理中客户端前的+号，复制启动命令
- 执行启动命令，linux直接执行即可，windows将./npc换成npc.exe用cmd执行

如果需要注册到系统服务可查看[注册到系统服务](https://ehang-io.github.io/nps/#/use?id=注册到系统服务)

### 配置
- 客户端连接后，在web中配置对应穿透服务即可
- 更多高级用法见[完整文档](https://ehang-io.github.io/nps/)

## 贡献
- 如果遇到bug可以直接提交至dev分支
- 使用遇到问题可以通过issues反馈
- 项目处于开发阶段，还有很多待完善的地方，如果可以贡献代码，请提交 PR 至 dev 分支
- 如果有新的功能特性反馈，可以通过issues或者qq群反馈



# windodws 安装笔记

## 下载

[releases](https://github.com/ehang-io/nps/releases) 中寻找合适的版本。

这里选择 windows windows_amd64_server 版本。

## 运行

解压，之后直接运行 nps.exe

日志：

```
2025/04/29 11:54:14.604 [I] [server.go:200]  tunnel task  start mode：httpHostServer port 0
2025/04/29 11:54:14.606 [I] [connection.go:71]  web management start, access port is 8080
2025/04/29 11:54:14.606 [I] [connection.go:62]  start https listener, port is 443
2025/04/29 11:54:14.606 [I] [connection.go:53]  start http listener, port is 80
```

## 访问

浏览器访问 http://localhost:8080/login/index

默认账密：admin/123


# 实际玩耍

后续实际使用体验一下，感觉不错。

可以内网搞一台自己的服务器。


# 参考资料

https://github.com/ehang-io/nps

* any list
{:toc}