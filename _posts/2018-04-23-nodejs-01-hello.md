---
layout: post
title:  NodeJs-01 Hello nodejs install windows11 npm/cnpm install
date:  2018-04-23 09:19:44 +0800
categories: [NodeJs]
tags: [js, nodejs, nodejs-learn, js-learn]
published: true
---

# NodeJs

[Node.js®](https://nodejs.org/en/) is a JavaScript runtime built on Chrome's V8 JavaScript engine. 

Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.
 
Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.


# 相关教程

> [正确学习 node](https://i5ting.github.io/How-to-learn-node-correctly/)

> [Node.js 教程](https://github.com/wangdoc/node-tutorial)

# 安装

- 安装

直接首页下载安装即可，比较简单。

- 测试确认

```
$ node -v
v6.2.2
```

## windows11 安装实战

> [node-v16.16.0-x64.msi](https://nodejs.org/dist/v16.16.0/node-v16.16.0-x64.msi)

调整默认的安装路径：

```
D:\Program Files\nodejs\
```

查看版本：

```
λ node -v
v16.16.0
```

## cnpm 安装处理

```
λ npm -v
npm WARN config global `--global`, `--local` are deprecated. Use `--location=global` instead.
8.11.0
```

npm 作为默认的安装路径，在国内可能会比较慢。

cnpm install 安装 

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# 入门案例

- hello-world.js

```js
console.log("hello, nodejs!");
```

- 编译

命令行直接执行

```
$   node hello-world.js
```

- 结果

```
$ node hello-world.js 
hello, nodejs!
```


# 版本更新

原来的版本相对较老，为了方便后期学习，更新至最新版，

## 教程

- 第一步，先查看本机 node.js 版本

```
$ node -v
```

- 第二步，清除 node.js 的 cache

```
$ sudo npm cache clean -f
```

- 第三步，安装 n 工具，这个工具是专门用来管理 node.js 版本的

```
$ sudo npm install -g n
```

- 第四步，安装最新版本的node.js

```
$ sudo n stable
```

- 第五步，再次查看本机的node.js版本：

```
$ node -v
```

## 实际操作

```
houbinbindeMacBook-Pro:~ houbinbin$ sudo npm cache clean -f
Password:
npm WARN using --force I sure hope you know what you are doing.
houbinbindeMacBook-Pro:~ houbinbin$ sudo npm install -g n
/usr/local/bin/n -> /usr/local/lib/node_modules/n/bin/n
/usr/local/lib
└── n@2.1.8 

houbinbindeMacBook-Pro:~ houbinbin$ sudo n stable

     install : node-v9.10.1
       mkdir : /usr/local/n/versions/node/9.10.1
       fetch : https://nodejs.org/dist/v9.10.1/node-v9.10.1-darwin-x64.tar.gz
######################################################################## 100.0%
   installed : v9.10.1

houbinbindeMacBook-Pro:~ houbinbin$ node -v
v9.10.1
```


# chat

## Q: 详细介绍一下 nodejs

Node.js 是一个开源、跨平台的JavaScript运行时环境，它允许开发者在服务器端运行JavaScript代码。Node.js 是基于Chrome V8 JavaScript引擎构建的，它的设计目标是提供一种高效、轻量级的方式来构建可扩展的网络应用程序。

以下是Node.js的一些关键特性和概念：

1. **异步和事件驱动**：Node.js 的大部分API都是异步的，即非阻塞的。这意味着Node.js基于事件循环机制，可以在执行I/O操作（如读写文件、数据库查询、网络请求等）时不被阻塞，从而使得Node.js非常适合处理大量并发连接。

2. **单线程**：尽管JavaScript在Node.js中是单线程执行的，但是通过事件循环和回调函数，Node.js可以处理大量并发，而不会因为线程创建和销毁的开销而降低性能。

3. **npm**：Node.js 附带了一个名为npm（Node Package Manager）的包管理器，它是世界上最大的开源库生态系统。开发者可以使用npm来安装、共享和管理依赖库。

4. **模块系统**：Node.js 有一个模块化的结构，使用CommonJS模块系统。每个模块都是一个单独的文件，可以导出对象、函数、变量等供其他模块使用。

5. **网络支持**：Node.js 提供了强大的网络支持，包括HTTP、TCP、UDP、DNS和TLS/SSL等，使其成为开发各种网络应用的理想选择。

6. **跨平台**：Node.js 可以在多个平台上运行，包括多种Unix变种、macOS、Windows等。

7. **Buffer和Stream**：Node.js 提供了Buffer类来处理二进制数据，以及Stream接口来处理数据流。

8. **社区驱动**：Node.js 拥有一个活跃的开发者社区，不断地为其贡献新的模块和工具。

9. **适用于构建多种应用**：Node.js 不仅适用于Web服务器和网站应用，还适用于IoT、实时通信、命令行工具、网络服务等。

10. **ES6+ 支持**：Node.js 支持大部分现代JavaScript特性，包括ES6+（ECMAScript 2015及以后版本）的类、模块、箭头函数等。

11. **安全性**：Node.js 提供了一些内置的安全性特性，并且社区维护着许多专注于安全性的模块。

12. **性能**：由于Node.js使用V8引擎，它在性能上通常比传统的服务器端JavaScript环境更快。

Node.js 的应用场景非常广泛，从小型的实时聊天应用到大型的在线平台，如LinkedIn、Netflix、PayPal、Uber等，都在使用Node.js。

要开始使用Node.js，你只需要从官方网站下载并安装它，然后就可以开始编写代码并运行你的应用程序了。

## windows 如何安装 node.js

在Windows系统中安装Node.js的步骤通常包括以下几个环节：

1. **下载Node.js安装包**：
   - 访问Node.js官方网站下载页面，选择适合Windows系统的预编译安装包。通常有两种版本可供选择：LTS（长期支持版）和Current（当前版）。LTS版本更稳定，适合生产环境；Current版本可能有最新的功能，但可能不如LTS版本稳定。

2. **运行安装程序**：
   - 下载完成后，双击`.msi`格式的安装包以运行安装程序。

3. **安装选项**：
   - 在安装过程中，可以选择安装路径。建议使用默认路径，也可以自定义安装路径。
   - 确保勾选了“Add to PATH”选项，这样可以在任何命令行窗口中直接运行Node.js和npm命令。

4. **环境变量配置**（如果“Add to PATH”未勾选）：
   - 打开系统属性，编辑环境变量，在系统变量中添加Node.js的安装路径。

5. **验证安装**：
   - 安装完成后，打开命令提示符（CMD）或PowerShell，输入`node -v`和`npm -v`来验证Node.js和npm是否安装成功，版本号将被显示出来。

6. **更换npm源**（可选）：
   - 由于某些原因，直接使用npm可能会比较慢，可以考虑更换npm的源为淘宝的npm镜像，以加快下载速度。可以通过命令`npm config set registry https://registry.npm.taobao.org/`来设置。

7. **安装版本管理器（可选）**：
   - 如果需要在Windows上管理多个版本的Node.js，可以使用如nvm-windows这样的版本管理器。

8. **安装其他工具或IDE（可选）**：
   - 根据个人喜好，可以安装如Visual Studio Code等集成开发环境，以便于开发。

按照这些步骤，你应该能够在Windows系统上成功安装Node.js。

如果在安装过程中遇到任何问题，可以参考官方文档或搜索相关的错误信息来解决。

### 版本测试

```
>node -v
v20.10.0

>npm -v
10.2.3
```

## 给出 nodejs 的入门例子

- 01-hello.js

```js
// 引入http模块
const http = require('http');

// 设置服务器监听的端口
const port = 3000;

// 创建HTTP服务器
const server = http.createServer((req, res) => {
  // 设置响应头，告知客户端我们使用的是HTTP协议版本1.1
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  // 向客户端发送响应
  res.end('Hello, World!\n');
});

// 监听指定端口
server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

### 启动

```
node 01-hello.js
```

启动日志：

```
Server running at http://localhost:3000/
```

浏览器访问 http://localhost:3000/

页面响应 

```
Hello, World!
```

## 如何系统的学习 node.js

系统地学习Node.js需要一个结构化的学习计划，以下是一些步骤和建议：

1. **理解JavaScript基础**：
   - 由于Node.js是基于JavaScript的，因此对JavaScript有扎实的理解是至关重要的。学习基本的语法、数据类型、函数、对象、原型继承等。

2. **熟悉Node.js环境和API**：
   - 阅读Node.js的官方文档，了解其核心模块，如`http`、`fs`（文件系统）、`path`、`events`等。

3. **设置开发环境**：
   - 安装Node.js和npm（Node包管理器），配置你的代码编辑器或IDE（如Visual Studio Code）。

4. **学习异步编程**：
   - 理解回调函数、Promises、async/await等异步编程模式，因为Node.js大量使用非阻塞I/O。

5. **掌握npm和包管理**：
   - 学习如何使用npm安装和管理项目依赖，以及如何发布自己的npm包。

6. **构建小型项目**：
   - 通过构建小型项目来实践你的知识，如命令行工具、简单的web应用等。

7. **学习中间件和框架**：
   - 研究Express.js等流行的Node.js框架，它们可以简化web应用的开发。

8. **理解数据库和存储**：
   - 学习如何使用Node.js与数据库交互，包括关系型数据库（如MySQL、PostgreSQL）和非关系型数据库（如MongoDB）。

9. **安全性知识**：
   - 学习基本的Web安全知识，如防止SQL注入、XSS和CSRF攻击。

10. **深入学习Node.js架构**：
    - 理解Node.js的事件循环、内存管理、进程和线程等高级主题。

11. **实际项目经验**：
    - 参与开源项目或实习，以获得实际的Node.js开发经验。

12. **构建更复杂的应用**：
    - 随着你技能的提升，尝试构建更复杂的应用，如实时通信应用、RESTful API、全栈应用等。

13. **性能优化**：
    - 学习如何分析和优化Node.js应用的性能。

14. **持续学习**：
    - 技术不断发展，持续关注Node.js社区的最新动态和最佳实践。

15. **资源利用**：
    - 利用在线资源，如教程、视频课程（比如Udemy、Coursera）、博客文章、技术论坛和Stack Overflow。

16. **构建个人作品集**：
    - 将你的项目部署到线上，并建立一个个人作品集，展示你的技能和项目。

17. **代码复审和版本控制**：
    - 学习使用Git进行版本控制，并通过Pull Request参与代码复审。

18. **测试**：
    - 学习单元测试、集成测试和端到端测试，确保你的代码质量。

19. **部署**：
    - 学习如何将Node.js应用部署到服务器，了解不同的托管服务和部署策略。

20. **软技能**：
    - 提升你的沟通、团队合作和问题解决能力。

记住，实践是学习的关键，因此要不断地编写代码、构建项目，并从错误中学习。此外，参与社区讨论和贡献开源项目也是提高技能的好方法。

## 有哪些可以系统学习 nodejs 的网站，给出网址

以下是一些可以系统学习Node.js的网站，以及它们的网址：

1. **Node.js 官方网站**：提供关于Node.js的全面信息，包括下载、文档和社区资源。
   - 网址：[https://nodejs.org/](https://nodejs.org/)

2. **Node.js 中文网**：提供Node.js的学习教程、下载安装指南和API文档。
   - 网址：[https://nodejs.cn/](https://nodejs.cn/)

3. **NodeSchool**：通过交互式工作坊来教授Node.js和其他技术。
   - 网址：[https://nodeschool.io/zh-cn/](https://nodeschool.io/zh-cn/)

4. **CNode（Node.js专业中文社区）**：一个中文社区，可以在这里找到Node.js的学习资源和与其他开发者交流。
   - 网址：[https://cnodejs.org/](https://cnodejs.org/)

5. **GitHub**：上面有许多开源的Node.js项目，可以边学习边实践。
   - 网址：[https://github.com/](https://github.com/)

6. **知乎**：可以找到许多关于Node.js学习的经验分享和问题解答。
   - 网址：[https://www.zhihu.com/](https://www.zhihu.com/)

7. **SegmentFault 思否**：一个开发者社区，提供问题解答和技术文章，可以找到Node.js相关的内容。
   - 网址：[https://segmentfault.com/](https://segmentfault.com/)

8. **dev.nodejs.cn**：Node.js的官方文档中文版，适合深入学习Node.js的API。
   - 网址：[https://dev.nodejs.cn/](https://dev.nodejs.cn/)

9. **Bilibili**：有许多免费的Node.js教学视频，适合通过视频学习的用户。
   - 网址：[https://www.bilibili.com/](https://www.bilibili.com/)

10. **Udemy、Coursera、edX**：这些在线学习平台提供专业的Node.js课程，部分课程可能需要付费。
    - Udemy：[https://www.udemy.com/](https://www.udemy.com/)
    - Coursera：[https://www.coursera.org/](https://www.coursera.org/)
    - edX：[https://www.edx.org/](https://www.edx.org/)

通过这些资源，你可以从基础到进阶系统地学习Node.js。

记住，实践是学习的最好方式，尝试构建自己的小项目，逐步提升技能水平。




* any list
{:toc}