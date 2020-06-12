---
layout:     post
title:      Potato Family
subtitle:   windows potato 提权
date:       2020-06-02
author:     Cooltige
header-img: img/post-bg-potato.jpg
catalog: false
tags:
    - windows
    - 提权
---

##	前言
本文只将juicy，sweet和pipe potato进行一个提权结果对比，不进行一个详细的提权原理的分析。
##    提权环境


*    Windows Server 2008 R2 Standard
*    Windows Server 2012 R2 Standard
*    Windows Server 2016 Datacenter
*    Windows Server 2019 Standard

##    Juicy Potato

Juicy Potato是一款Windows系统的本地提权工具，是在工具RottenPotatoNG的基础上做了扩展，适用条件更广

利用的前提是获得了SeImpersonate或者SeAssignPrimaryToken权限，通常在webshell下使用

Juicy Potato的下载地址：

https://github.com/ohpe/juicy-potato

本文所使用的juicy potato是修改后版本。
##    Sweet Potato

repo: [https://github.com/CCob/SweetPotato](https://github.com/CCob/SweetPotato)

COM/WinRM/Spoolsv的集合版，也就是Juicy/PrintSpoofer

这里用的版本为@uknowsec大牛修改后的版本：

[https://github.com/uknowsec/SweetPotato](https://github.com/uknowsec/SweetPotato)

##    Pipe Potato

首先，攻击者拥有一个服务用户，这里演示采用的是IIS服务的用户。攻击者通过pipeserver.exe注册一个名为pipexpipespoolss的恶意的命名管道等待高权限用户来连接以模拟高权限用户权限，然后通过spoolssClient.exe迫使system用户来访问攻击者构建的恶意命名管道，从而模拟system用户运行任意应用程序

原文地址：[首发披露！pipePotato：一种新型的通用提权漏洞](https://mp.weixin.qq.com/s?__biz=MzA5ODA0NDE2MA==&mid=2649721577&idx=1&sn=63492135184603b429aa582ba1aaae14&chksm=888cba86bffb33906f6240256fc1221c2d7f8634f585945300f06c7b41fc89c8d842938e47ad&scene=126&sessionid=1588771605&key=b152a177cb32a70e1b306b467192c663cd1fdce048ef5fd610ecd579442d68f99b42489b7b4a236b4225dcdfd2e5e1391b409f72d49b1bb0f87dd271417a0b350c0b30dec106acc36f486e31c6028fca&ascene=1&uin=MTA4NDQ2OTQyNQ%3D%3D&devicetype=Windows+10+x64&version=62090070&lang=zh_CN&exportkey=A1nxB%2BVmcEwCBPW%2FaI67iwA%3D&pass_ticket=0xebiPfVsRrXZEc9Svu4bjBx%2FQndL34gmcp6e9jrjx4kbPtNEBkXSNC36M9%2FiP2u)

#    提权结果
##    Windows Server 2008 R2 Standard

当前权限

![](/img/potato_family/576a1a0b-7d4e-490a-a10e-3cce2fa9da9a.png)

*    Juicy Potato

![](/img/potato_family/e0053f5d-c3b6-41ca-9a93-2d505c170c9d.png)

*    Sweet Potato

![](/img/potato_family/a26002f3-6576-4522-bcf7-d41340dd2068.png)

*    Pipe Potato

![](/img/potato_family/49b9511f-190e-438d-ad07-1e757aa3352d.png)


##    Windows Server 2012 R2 Standard

当前权限

![](/img/potato_family/41d08f0d-043a-47a3-9d32-14ac4c1fa71a.png)

*    Juicy Potato

![](/img/potato_family/ec44012d-9706-4be2-984d-b50d4b992aed.png)

*    Sweet Potato

![](/img/potato_family/a3134063-f195-448b-bc96-82800118ebe0.png)

*    Pipe Potato

![](/img/potato_family/91c9c6e8-4c27-4a28-abe0-11044689233a.png)


##    Windows Server 2016 Datacenter

当前权限

![](/img/potato_family/713dd477-fdf9-40d5-bf38-ecd7973f1f02.png)

*    Juicy Potato

![](/img/potato_family/fbcbe8f7-4e6b-445b-a64d-dd9fe4905bea.png)

*    Sweet Potato

![](/img/potato_family/33e7ccb1-5e58-428a-90a4-8d40d62578fa.png)

*    Pipe Potato

![](/img/potato_family/1e4e4608-333e-4d47-a16a-10c7a4524021.png)

##    Windows Server 2019 Standard

当前权限

![](/img/potato_family/81585525-ad83-4fbf-b9c7-d8c465c55b77.png)

*    Juicy Potato

![](/img/potato_family/1b58e46b-896d-4925-8a8e-2e362cc1dbdc.png)

*    Sweet Potato

![](/img/potato_family/e40a765a-2b53-4dff-9287-d8a77e51d8de.png)

*    Pipe Potato

![](/img/potato_family/16e5fb76-69b0-442b-9212-229c5556b974.png)

#    总结

![](/img/potato_family/b40f50b3-71c4-433e-8158-348e6abe56e6.png)

**本站提供的所有内容仅供学习、分享与交流，禁止用于非法途径，通过使用本站内容随之而来的风险与本站无关**