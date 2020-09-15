---
layout:     post
title:      Cobalt Strike 不出网机器上线方式
subtitle:   Cobalt Strike
date:       2020-09-15
author:     Cooltige
header-img: img/post-bg-cs.jpg
catalog: false
tags:
    - Cobalt Strike
    - 内网
---

##    实验环境

实验主机

winserver 2003 双网卡机器
192.168.204.134
10.10.10.128

win7_1    不出网机器
10.10.10.129

win7_2    不出网机器 只能通过win7_1连接
10.10.10.130

###    windows/beacon_tcp/bind_tcp

创建监听器，生成马，上线双网卡机器winerver 2003。

![](/img/Cobalt_Strike_beacon/2135715a-a4b6-4959-a3c2-7331fca4eb4f.png)

![](/img/Cobalt_Strike_beacon/8b1b2635-b67b-4ae7-920e-20a6132b6cad.png)

![](/img/Cobalt_Strike_beacon/219a3592-8f9d-4b68-8200-d6233891f8b6.png)

03为双网卡机器，win7_1不出网且只能与03通信，所以创建新监听。

![](/img/Cobalt_Strike_beacon/4ec1ad43-d017-4136-8e04-24180252599f.png)

通过新监听器生成对应的马。

![](/img/Cobalt_Strike_beacon/a744e776-bfec-47af-ad88-4cbf3e10231f.png)

通过任意手段在目标上执行，当目标执行过后，回到03beacon，执行connect 10.10.10.129，完成不出网机器上线。

![](/img/Cobalt_Strike_beacon/2ddbfbe3-8d13-49a7-b4e7-4b922d3774a3.png)

win7_2只能通过win7_1进行连接，所以这个地方想要上线win7_2，必须再创建一个监听器。

![](/img/Cobalt_Strike_beacon/f779db3d-c029-48a6-a8c8-29e1614aa630.png)

通过新监听器创建木马，然后在目标上执行。步骤和上面差不多。

目标上执行后，再win7_1上执行connect 10.10.10.130，即可完成win7_2上线。

![](/img/Cobalt_Strike_beacon/9921cccf-9117-4572-ac10-3ef2b26e4f0e.png)

最后展示下网络拓扑图。

![](/img/Cobalt_Strike_beacon/5737a291-8936-4531-93d5-717bdaf940bb.jpg)

###    windows/beacon_smb/bind_pipe

同上方法一样，第一步上一台03入口机器。

![](/img/Cobalt_Strike_beacon/9001dbc8-60ba-41e5-bf7e-2b311441dbbd.png)

创建smb监听器，这个smb监听器ip和port没用，乱填都行。

![](/img/Cobalt_Strike_beacon/10ced509-4595-49f6-9b54-574461227eb4.png)

![](/img/Cobalt_Strike_beacon/67d55b7e-803a-46db-96f0-9e4574958111.png)

这里介绍两种上线方式

####    第一种方式

通过生成一个smb监听器对应的马，在目标上执行smb监听器生成的马。
目标上通过什么权限执行的马，我们获得的就是什么权限。

![](/img/Cobalt_Strike_beacon/7f1a1a03-fd10-4666-9f57-1f0f2f37359e.png)

通过 03 beacon执行命令:link 10.10.10.129 。发现 10.129 并没有上线，这里需要利用10.129的hash或者账号密码制作一个token。然后就可以完成上线

![](/img/Cobalt_Strike_beacon/5f5cf6f0-390d-428e-bf49-f4139dc4ff07.png)

在制作token前，执行命令`rev2self`，清除当前token。

![](/img/Cobalt_Strike_beacon/e725c085-b48d-40dd-8955-61efb7ff518a.png)

然后制作token，完成上线。

![](/img/Cobalt_Strike_beacon/451b18cc-41b9-4cf7-ad52-47ac2bf33fc6.png)

这里的token制作，不管是什么权限的用户都行。

####    第二种方式

通过psexec进行上线。

选中目标，然后psexec，进行上线，这里利用的账号必须为administrator才可以，其他管理员账号是不行的。

![](/img/Cobalt_Strike_beacon/a5a72405-64a6-42e8-a18a-1d2c8ded93d3.png)

利用root用户进行psexec，选中smb监听器上线

![](/img/Cobalt_Strike_beacon/5fc9973e-fdb8-46b3-99e4-d184e3869702.png)

无法上线

![](/img/Cobalt_Strike_beacon/08fac025-5b7d-408f-a765-99bd70633c21.png)

利用administrator用户进行psexec上线。

![](/img/Cobalt_Strike_beacon/f44015ab-2715-4753-826f-b74845ea2512.png)

![](/img/Cobalt_Strike_beacon/afce24df-a78e-4374-b673-a732a26de558.png)

完成上线，这种方法由于是直接利用administrator账号进行psexec上线，所以得到的beacon一定是system。

![](/img/Cobalt_Strike_beacon/41972c55-b87a-4829-8f15-8c806ebbb6d5.png)

---

win7_2上线，按照上面2种方法依葫芦画瓢就行了。

![](/img/Cobalt_Strike_beacon/45f71c6d-04c1-428c-bcb2-2624c18aeb20.png)

网络拓扑图

![](/img/Cobalt_Strike_beacon/032ed658-6223-4340-8190-b2d99e05c5a6.jpg)

这种监听器上线，我们是利用的link进行连接。在目标上查看网络连接。

![](/img/Cobalt_Strike_beacon/7a78445f-7fe6-4f97-9f68-cedaf8b4b0f3.png)

利用unlink断开连接。

![](/img/Cobalt_Strike_beacon/da038f8a-b68e-47b1-8127-27d8a9e2eff1.png)

可以看到图标的样子发生了改变。这时候再查看网络连接。

![](/img/Cobalt_Strike_beacon/c6e23c4f-ca57-4814-813d-521157388119.png)

如果我们想再次拉起beacon，不用重复上面的步骤，只需要再次执行link命令就行了。

![](/img/Cobalt_Strike_beacon/2df40def-9e88-459d-8520-732b23807e1a.png)

查看网络连接，再次与03进行连接。

![](/img/Cobalt_Strike_beacon/a3889990-7533-445d-aa2d-b659e93b4d1b.png)

###    windows/beacon_reverse_tcp

此方法主要为反向链接，目标不出网机器去链接我们的跳板机，这种方法的好处就在于，每一台内网机器都可以创建一个reverse_tcp监听，可以加大溯源的难度。

同样的首先上线入口机器03 server

![](/img/Cobalt_Strike_beacon/f8361216-8b4a-4356-a6ee-62fa618da28e.png)

然后选中我们的入口机器，创建新的监听，此处不能直接在配置监听处创建，必须要选中一个beacon会话进行创建。

![](/img/Cobalt_Strike_beacon/d8cc8b62-b74c-4159-9f42-c14dbbf9294a.png)

下方填入目标连接03机器的ip和port，点击创建即可。

![](/img/Cobalt_Strike_beacon/27a7b8ad-2117-4b49-bcca-4132f2d03d4b.png)

通过生成可执行文件，在目标上执行，完成上线。

![](/img/Cobalt_Strike_beacon/1191a875-682c-4285-a99b-efca4de2c315.png)

![](/img/Cobalt_Strike_beacon/e66806be-d0e2-47d2-bd88-bd7bd1afc0b5.png)

查看win7_1的网络连接可以看到与我们入口机器4444端口进行连接。

![](/img/Cobalt_Strike_beacon/cc198c53-6483-4d1e-8e14-456827d4ef11.png)

同样的方法，一葫芦画瓢在win7_1上创建监听。

![](/img/Cobalt_Strike_beacon/f160b8b4-a3c6-4ddd-bfd7-9d7844ecbc1e.png)

生成可执行文件，让目标上线。

![](/img/Cobalt_Strike_beacon/3185af48-e930-41fe-8020-a0283fe72a14.png)

查看网络连接。

![](/img/Cobalt_Strike_beacon/d6a72295-f6ed-4920-a8da-26e7750f1ef8.png)

最后查看下网络拓扑图。

![](/img/Cobalt_Strike_beacon/c7b1c176-e2b7-49c6-a98f-5c0480bed06c.jpg)

箭头都是指向创建反向监听的机器。

##    总结

面对不同的情况，选中不同的方式，方可事半功倍。

**本站提供的所有内容仅供学习、分享与交流，禁止用于非法途径，通过使用本站内容随之而来的风险与本站无关**