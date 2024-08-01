---
categories: powershell
layout: post
permalink: /powershell/分享原创powershell脚本小工具ctracert.ps1
---

# ----------【脚本介绍】-----------

脚本名称：ctracert.ps1
软件名称：灰主牛 跟踪路由 归属地版 V1.0
脚本作用：1跟踪路由。2显示归属地。（注意不带显示时间功能）
脚本作者=我=powershell传教士
用法：ctracert.ps1 域名或ip
完成日期：2016-02-03

QQWry.NET.dll 是供.net调用纯真ip数据文件的dll库。来源于http://hjf1223.cnblogs.com/ ，感谢作者阿布（mvp）

2016-05-29 1.01版 发布
新增序号，超时的ip地址的处理，更新ip库。

2018-02-28 1.02 更新ip库

# ----------【界面抓图】-----------

![](https://images2015.cnblogs.com/blog/456691/201602/456691-20160203214419147-889592696.jpg)

----------【升级ip地址库】-----------

你可以定期去 www.cz88.net 下载

----------【系统需求】-----------
win8.1，win10，win2012r2

----------【下载地址】-----------

[https://files.cnblogs.com/files/piapia/gsd\_tracert\_v102.zip](https://files.cnblogs.com/files/piapia/gsd_tracert_v102.zip)

脚本，调用库，纯真ip库

----------【win7 + powershell 2.0 版本 的脚本】-----------

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
 1 #脚本作者： 史瑞克    http://blog.csdn.net/shrekz
 2 #感谢分享！
 3 param($dm)
 4 $tarcrout=tracert -d $dm
 5 $tarcrout=$tarcrout|out-string
 6 $null=[system.reflection.Assembly]::LoadFrom("C:\Users\dell\Downloads\gsd_tracert_v1\安装\QQWry.NET.dll")
 7 $QQWry=New-Object QQWry.NET.QQWryLocator("C:\Users\dell\Downloads\gsd_tracert_v1\安装\QQWry.Dat")
 8 [regex]::Replace($tarcrout,'(\d{1,3}\.){3}\d{1,3}(?=\s)',{param($ip)
 9 $addinfo=$QQWry.Query($ip)
10 "$ip $($addinfo.Country) $($addinfo.Local)"
11 }
12 )
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

![](https://images2015.cnblogs.com/blog/456691/201604/456691-20160428191726048-412956613.jpg)

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [归属地](https://www.cnblogs.com/piapia/tag/%E5%BD%92%E5%B1%9E%E5%9C%B0/) , [归属地查询](https://www.cnblogs.com/piapia/tag/%E5%BD%92%E5%B1%9E%E5%9C%B0%E6%9F%A5%E8%AF%A2/) , [tracert](https://www.cnblogs.com/piapia/tag/tracert/)



