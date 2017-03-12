---
layout:     post
title:      "WMIC命令使用详解"
subtitle:   "在WMIC命令行提示符上，命令以交互的方式执行"
date:       2017-03-01
author:     "Chenyan"
header-img: "img/post-bg-wmic.jpg"
catalog:    false
tags:
    - Windows
---

>执行“wmic”命令启动WMIC命令行环境。这个命令可以在XP或 .NET Server的标准命令行解释器（cmd.exe）、Telnet会话或“运行”对话框中执行。这些启动方法可以在本地使用，也可以通过.NET Server终端服务会话使用。

####使用模式

使用模式分为交互模式(Interactive mode)和非交互模式(Non-Interactive mode)

* 输入cmd进入命令行，键入wmic后出现wmic:root\cli>时你就可以输入命令了，在这里面输入命令来完成指令。exit是退出交互模式。
* 用wmic后面直接跟命令运行，如wmic process就显示所有的进程了。

####基本命令

**显示出BIOS信息：** `wmic bios list full`

大家可能注意到了上面命令行中还有两个参数list和full。list决定显示的信息格式与范围，它有<font style="color:red">**Brief、Full、Instance、 Status、System、Writeable**</font>等多个参数，full只是它的一个参数，也是list的缺省参数，表示显示所有的信息。其他几个参数顾名思义，如Brief表示只显示摘要信息，Instance表示只显示对象实例，Status表示显示对象状态，Writeable表示只显示该对象的可写入的属性信息等。

**查看cpu型号：** `cpu get name`

**查看系统开机启动项：** `startup`

**DataFile 管理：** `datafile`
* 查找e盘下test目录(不包括子目录)下的cc.cmd文件
`datafile where "drive='e:' and path='\\test\\' and FileName='cc' and Extension='cmd'" list`
* 删除e盘下文件大小大于10M的.cmd文件
`datafile where "drive='e:' and Extension='cmd' and FileSize>'10000000'" call delete`
* 删除e盘下test目录(不包括子目录)下的非.cmd文件
`datafile where "drive='e:' and Extension<>'cmd' and path='test'" call delete`
* 改名c:\hello.txt为c:\test.txt
`datafile "c:\\hello.txt" call rename c:\test.txt`
* 查找h盘下目录含有test,文件名含有perl,后缀为txt的文件
`datafile where "drive='h:' and extension='txt' and path like '%\\test\\%' and filename like '%perl%'" get name`
* 删除e盘下test目录(不包括子目录)下的非.cmd文件
`datafile where "drive='e:' and Extension<>'cmd' and path='test'" call delete`

**监视器管理：** `desktopmonitor where Status='ok' get ScreenHeight,ScreenWidth`

**获取物理磁盘型号大小等：** `diskdrive get caption,size,interfacetype`

**环境变量：** `environment`

**进程管理：** `process list brief`
* 新建notepad进程
`process call create "C:\Program Files\Tencent\QQ\QQ.exe"`
* 结束notepad进程
`process where "name='notepad.exe'" call terminate`
