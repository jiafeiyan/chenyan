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

#### WMIC命令参数帮助参考:
命令 | 含义
--------|--------
ALIAS                  |  访问本地机器上的别名 
BASEBOARD              |  基板 (也叫母板或系统板) 管理。 
BIOS                   |  基本输入/输出服务 (BIOS) 管理。 
BOOTCONFIG             |  启动配置管理。 
CDROM                  |  CD-ROM 管理。 
COMPUTERSYSTEM         |  计算机系统管理。 
CPU                    |  CPU 管理。 
CSPRODUCT              |  SMBIOS 的计算机系统产品信息。 
DATAFILE               |  DataFile 管理。 
DCOMAPP                |  DCOM 程序管理。 
DESKTOP                |  用户桌面管理。 
DESKTOPMONITOR         |  监视器管理。 
DEVICEMEMORYADDRESS    |  设备内存地址管理。 
DISKDRIVE              |  物理磁盘驱动器管理。 
DISKQUOTA              |  NTFS 卷磁盘空间使用情况。 
DMACHANNEL             |  直接内存访问(DMA)频道管理。 
ENVIRONMENT            |  系统环境设置管理。 
FSDIR                  |  文件目录系统项目管理。 
GROUP                  |  组帐户管理。 
IDECONTROLLER          |  IDE 控制器管理。 
IRQ                    |  间隔请求线 (IRQ) 管理。 
JOB                    |  提供对使用计划服务安排的工作的访问。 
LOADORDER              |  定义执行依存的系统服务管理。 
LOGICALDISK            |  本地储存设备管理。 
LOGON                  |  登录会话。 
MEMCACHE               |  缓存内存管理。 
MEMLOGICAL             |  系统内存管理 (配置布局和内存可用性)。 
MEMPHYSICAL            |  计算机系统物理内存管理。 
NETCLIENT              |  网络客户端管理。 
NETLOGIN               |  (某一用户的)网络登录信息管理。 
NETPROTOCOL            |  协议 (和其网络特点) 管理。 
NETUSE                 |  活动网络连接管理。 
NIC                    |  网络界面控制器 (NIC) 管理。 
NICCONFIG              |  网络适配器管理。 
NTDOMAIN               |  NT 域管理。 
NTEVENT                |  NT 事件日志的项目 
NTEVENTLOG             |  NT 时间日志文件管理。 
ONBOARDDEVICE          |  母板(系统板)内置普通设适配器设备的管理。 
OS                     |  已安装的操作系统管理。 
PAGEFILE               |  虚拟内存文件对调管理。 
PAGEFILESET            |  页面文件设置管理。 
PARTITION              |  物理磁盘分区区域的管理。 
PORT                   |  I/O 端口管理。 
PORTCONNECTOR          |  物理连接端口管理。 
PRINTER                |  打印机设备管理。 
PRINTERCONFIG          |  打印机设备配置管理。 
PRINTJOB               |  打印工作管理。 
PROCESS                |  进程管理。 
PRODUCT                |  安装包任务管理。 
QFE                    |  快速故障排除。 
QUOTASETTING           |  设置卷的磁盘配额信息。 
RECOVEROS              |  当操作系统失败时，将从内存收集的信息。 
REGISTRY               |  计算机系统注册表管理。 
SCSICONTROLLER         |  SCSI 控制器管理。 
SERVER                 |  服务器信息管理。 
SERVICE                |  服务程序管理。 
SHARE                  |  共享资源管理。 
SOFTWAREELEMENT        |  安装在系统上的软件产品元素的管理。 
SOFTWAREFEATURE        |  SoftwareElement 的软件产品组件的管理。 
SOUNDDEV               |  声音设备管理。 
STARTUP                |  用户登录到计算机系统时自动运行命令的管理。 
SYSACCOUNT             |  系统帐户管理。 
SYSDRIVER              |  基本服务的系统驱动程序管理。 
SYSTEMENCLOSURE        |  物理系统封闭管理。 
SYSTEMSLOT             |  包括端口、插口、附件和主要连接点的物理连接点管理。
TAPEDRIVE              |  磁带驱动器管理。 
TEMPERATURE            |  温度感应器的数据管理 (电子温度表)。 
TIMEZONE               |  时间区域数据管理。 
UPS                    |  不可中断的电源供应 (UPS) 管理。 
USERACCOUNT            |  用户帐户管理。 
VOLTAGE                |  电压感应器 (电子电量计) 数据管理。 
VOLUMEQUOTASETTING     |  将某一磁盘卷与磁盘配额设置关联。 
WMISET                 |  WMI 服务操作参数管理。 