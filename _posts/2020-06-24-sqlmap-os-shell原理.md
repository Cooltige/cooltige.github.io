---
layout:     post
title:      Potato Family
subtitle:   Sqlmap --os-shell原理
date:       2020-06-024
author:     Cooltige
header-img: img/post-bg-sqlmap-osshell.jpg
catalog: false
tags:
    - database
    - web
---

##    前言

当数据库为MySQL，PostgreSQL或Microsoft SQL Server，并且当前用户有权限使用特定的函数。

在mysql、PostgreSQL，sqlmap上传一个二进制库，包含用户自定义的函数，sys_exec()和sys_eval()。那么他创建的这两个函数可以执行系统命令。

在Microsoft SQL Server，sqlmap将会使用xp_cmdshell存储过程，如果被禁（在Microsoft SQL Server 2005及以上版本默认禁制），sqlmap会重新启用它，如果不存在，会自动创建

接下来我会通过注入、SQLSERVER数据库、Mysql数据库进行介绍os shell原理。

##    注入
必要条件：
*    拥有网站的写入权限
*   Secure_file_priv参数为空或者为指定路径。

普通注入--os-shell主要是通过上传一个sqlmap的马，然后通过马来进行命令执行。

---
#### 测试环境:
操作系统： Microsoft Windows Server 2012 Standard
数据库：Mysql 5.1.60
脚本语言：PHP 5.4.45
Web容器：Apache 2.4.39

利用sqlmap进行注入检测。

![](/img/sqlmap-os-shell/24cf2202-eb49-431b-b4c1-d4452eb61596.jpg)

然后执行`--os-shell`。

![](/img/sqlmap-os-shell/649f5a9f-bdb5-435a-8338-b6e0ebdc89b4.jpg)

**这个时候sqlmap主要做了三件事情：**

1、进行目标的一个基础信息的探测。
2、上传shell到目标web网站上。
3、退出时删除shell。

---

wireshark捕获数据包，只查看http数据包。

![](/img/sqlmap-os-shell/dcb7b845-2d6d-4151-9216-f9d84d581566.png)

**1、sqlmap上传一个上传功能的马。**

![](/img/sqlmap-os-shell/38d50fc9-a844-4682-a9b1-49b2c6fbd749.png)
![](/img/sqlmap-os-shell/e273a361-8a37-4e4b-a494-ad845aa3d7c6.png)
![](/img/sqlmap-os-shell/2cf24800-d33f-4e49-957c-7e64139a54b6.png)

追踪http流可以看到内容被url编码了，解开后可以看到是通过into outfile进行文件的写入。
马的内容进行了16进制编码，解开后查看代码就可以发现是一个上传功能的马。

**2、通过上传的马进行shell的上传。**

![](/img/sqlmap-os-shell/7af7f6d9-873f-405e-8887-e02c091899fb.png)

追踪http流可以看到body为shell的内容。

**3、shell传参进行命令执行。**

![](/img/sqlmap-os-shell/9ad5ab85-e0fc-43d3-82d3-cdbc3ce3808f.png)

**4、删除shell。**

![](/img/sqlmap-os-shell/d6571d26-f458-461e-8e65-88d177a51051.png)

执行命令删除shell。

---
##    Database

数据库支持外连，通过Sqlmap执行`--os-shell`获取shell。

###    Sqlserver
必要条件：
*    数据库支持外连
*    数据库权限为SA权限

Sqlserver --os-shell主要是利用`xp_cmdshell`扩展进行命令执行。

---
#### 测试环境:
操作系统：Microsoft Windows Server 2016 Datacenter
数据库：Microsoft SQL Server 2008

利用Sqlmap进行数据库连接。

`sqlmap -d "mssql://uset:password@ip:port/dbname"`

![](/img/sqlmap-os-shell/369b381d-8cc8-405b-a4b0-c6c529029c4b.png)

sqlmap默认不自带`pymssql`，需要手动下载。

执行命令`python -m pip install pymssql`下载，然后连接成功。

![](/img/sqlmap-os-shell/399087ff-6585-4627-8e93-8ccf475b2274.png)

执行`--os-shell`。

![](/img/sqlmap-os-shell/12482de9-05f3-4533-9836-13db341421bc.png)

**这个时候sqlmap主要做了三件事情：**

1、识别当前数据库类型，然后打印出来。
2、检测是否为数据库dba，也就是查看是否为sa权限。
3、检测是否开启了xp_cmdshell，如果没有开启sqlmap就会尝试开启。

这个地方Sqlmap未能成功开启xp_cmdshell。

执行`--sql-shell`手动开启。
手动开启语句：
```
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

![](/img/sqlmap-os-shell/88daf489-d16c-4fc5-ab88-4f1ddefda5bf.png)

在执行`RECONFIGURE;`时sqlmap报语法错误。

写一个python脚本调用下载的pymssql模块进行排错。

可以执行`select @@version;`命令

![](/img/sqlmap-os-shell/1201706e-d38f-4c17-922a-f8751e9ecfa7.png)

执行`RECONFIGURE;`命令的时候的报错和sqlshell执行时的报错一样。

![](/img/sqlmap-os-shell/ba327e65-08fe-4237-8ccf-624d42b7a259.png)

由于sqlmap调用的是pymssql模块进行数据库的链接，所以这个地方要开启xp_cmshell，就必须利用其他工具进行开启。利用navicat进行数据库连接。

![](/img/sqlmap-os-shell/134f838f-7d50-4884-bb1e-0462f33f5eb7.png)

然后执行命令开启xp_cmdshell。

![](/img/sqlmap-os-shell/5dd673ec-f435-4f23-88a1-c8ae458fedd7.png)

开启后，可以在navicat里面执行命令，或者sqlmap使用`--os-shell`进行命令执行。

![](/img/sqlmap-os-shell/3f644dfa-41e7-4f9f-a1f2-93734e1925de.png)
![](/img/sqlmap-os-shell/c439a911-7d6e-4e2b-8c9a-b9f57a4c226e.png)

若从一开始就使用navicat或其他工具进行数据库链接的话，就需要手动查看是否为dba，是否开启了`xp_cmdshell`扩展进程。

```
select IS_SRVROLEMEMBER('sysadmin')
```
查看是否为SA

```
select count(*) from master.dbo.sysobjects where xtype='x' and name='xp_cmdshell';
```

查看是否存在`xp_cmdshell`扩展进程，显示1为存在。

查询完毕后，和上面的操作依葫芦画瓢就行了。

---
wireshark捕获数据包，追踪TCP流。

![](/img/sqlmap-os-shell/46fba74c-c053-49c0-aca8-f97b5d7938f6.png)

我将代码复制到文本中，替换掉`.`。

![](/img/sqlmap-os-shell/b49b7293-cd82-4933-99a5-4ff66f22f375.png)

sqlmap会在执行我们输入的命令之前执行`ping -n 10 127.0.0.1`和`echo 1`,也就是①②，③开始就是我们输入的命令，这里命令都被进行16进制编码了。


###    Mysql

*    数据库支持外连
*    Secure_file_priv参数为空或者为指定路径。
*    对mysql目录存在写入权限。
*    针对版本大于5.1,需要存在/lib/plugin目录。

Mysql --os-shell主要利用的原理是通过udf执行命令，在[Mysql Udf提权](https://cooltige.github.io/2020/06/02/Mysql-Udf%E6%8F%90%E6%9D%83/)这一篇文章中我讲得比较详细了，可以去看看。

----
#### 测试环境:
操作系统：Microsoft Windows Server 2012 Standard
数据库：Mysql 5.1.60

利用Sqlmap进行数据库连接。

![](/img/sqlmap-os-shell/8b52fdf7-9156-4e51-8a96-0a974039b119.jpg)

安装`pymysql`后再次进行连接，连接后会显示数据库大概的版本。

![](/img/sqlmap-os-shell/5cc11e83-ed74-48f9-b850-5693646d31e1.jpg)

执行`sqlmap -d --os-shell`。

![](/img/sqlmap-os-shell/71349b8a-49a4-488e-b741-32fedca18b51.jpg)

**这个时候sqlmap主要做了五件事情：**

1、连接Mysql数据库并且获取数据库版本。
2、检测是否为数据库dba。
3、检测`sys_exec`和`sys_eval`2个函数是否已经被创建了。
4、上传dll文件到对应目录。
5、用户退出时默认删除创建的`sys_exec`和`sys_eval`2个函数。

---
wireshark捕获数据包，追踪TCP流。
这里我就直接贴@xz[老锥](https://xz.aliyun.com/u/5054)的图，他分析的很详细。

![](/img/sqlmap-os-shell/20200318155737-223e571e-68ee-1.png)

##	写在最后

*	本文若有差错,请务必斧正

**本站提供的所有内容仅供学习、分享与交流，禁止用于非法途径，通过使用本站内容随之而来的风险与本站无关**
