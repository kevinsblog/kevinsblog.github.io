---
layout: post
title: 使用Jenkins
tags: [Jenkins]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

[原文链接](https://blog.csdn.net/miss1181248983/article/details/82840006)

## 开发流程与CI
开发工作流程分为以下几个阶段：

编码 → 构建 → 集成 → 测试 → 交付 → 部署
![开发工作流程](https://img-blog.csdn.net/20180925151340942?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pc3MxMTgxMjQ4OTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

持续集成(Continuous Integration)、持续交付(Continuous Delivery)和持续部署(Continuous Deployment)有着不同的软件自动化交付周期。

### 持续集成

上面整个流程中最重要的组成部分就是持续集成（Continuous integration，简称CI）。持续集成指的是，频繁地（一天多次）将代码集成到主干。将软件个人研发的部分向软件整体部分交付，频繁进行集成以便更快地发现其中的错误。

![持续集成](https://img-blog.csdn.net/20180925151403503?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pc3MxMTgxMjQ4OTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

note: 持续集成并不能消除Bug，而是让它们非常容易发现和改正。持续集成的目的，就是让产品可以快速迭代，同时还能保持高质量。它的核心措施是，代码集成到主干之前，必须通过自动化测试。只要有一个测试用例失败，就不能集成。

![CI Server](http://s1.51cto.com/images/20171221/1513819848521564.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

### 持续交付

持续交付（Continuous delivery）指的是，频繁地将软件的新版本，交付给质量团队或者用户，以供评审。如果评审通过，代码就进入生产阶段。

持续交付在持续集成的基础上，将集成后的代码部署到更贴近真实运行环境的「类生产环境」(production-like environments)中。持续交付优先于整个产品生命周期的软件部署，建立在高水平自动化持续集成之上。

![持续交付](https://img-blog.csdn.net/20180925151424171?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pc3MxMTgxMjQ4OTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

note: 持续交付可以看作持续集成的下一步。它强调的是，不管怎么更新，软件是随时随地可以交付的。

### 持续部署

持续部署（continuous deployment）是持续交付的下一步，指的是代码通过评审以后，自动部署到生产环境。持续部署的目标是，代码在任何时刻都是可部署的，可以进入生产阶段。持续部署的前提是能自动化完成测试、构建、部署等步骤。

![持续部署](https://img-blog.csdn.net/20180925151442901?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pc3MxMTgxMjQ4OTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

总的来说，持续集成、持续交付、持续部署提供了一个优秀的 DevOps 环境。对于整个开发团队来说，能很大地提升开发效率，好处与挑战并行。无论如何，频繁部署、快速交付以及开发测试流程自动化都将成为未来软件工程的重要组成部分。

## Jenkins概念

Jenkins是一个**开源的、可扩展的持续集成、交付、部署（软件/代码的编译、打包、部署）**的基于web界面的平台。允许持续集成和持续交付项目，无论用的是什么平台，可以处理任何类型的构建或持续集成。

[官网](https://jenkins.io/)
[官方文档](https://jenkins.io/doc/)

Jenkins特性：
```
开源的java语言开发持续集成工具，支持CI，CD；

易于安装部署配置：可通过yum安装,或下载war包以及通过docker容器等快速实现安装部署，可方便web界面配置管理；

消息通知及测试报告：集成RSS/E-mail通过RSS发布构建结果或当构建完成时通过e-mail通知，生成JUnit/TestNG测试报告；

分布式构建：支持Jenkins能够让多台计算机一起构建/测试；

文件识别:Jenkins能够跟踪哪次构建生成哪些jar，哪次构建使用哪个版本的jar等； 

丰富的插件支持:支持扩展插件，你可以开发适合自己团队使用的工具，如git，svn，maven，docker等。
```