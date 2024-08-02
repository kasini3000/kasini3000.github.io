---
categories: powershell
layout: post
permalink: /powershell/powershell判断操作系统版本命令
---

powershell 传教士 原创文章。始于 2015-12-15 允许转载，但必须保留名字和出处，否则追究法律责任

一 前言

判断操作系统版本，是个老话题，bat、vbs中都有例子，这本不是重要问题，我感脚不值得写帖。
但是捏，随着win版本的增多，powershell版本碎片化越来越严重了，所以《用powershell判断操作系统版本》
才越来越重要了。也就是说，使用稍微偏一点的powershell命令前，你必须判断操作系统版本。或者，强烈
推荐大家用最新版本win服务器，win客户机玩powershell。

二 各种方法优缺点

cmd+ver法：
貌似无法无别服务器和客户机。

wmi法：
推荐

systeminfo法：
对中文系统，英文系统的命令有区别。

貌似无法无别服务器和客户机。

[Environment]: :OSVersion.VersionString法：
# 三 翠花上命令

判断win7
(cmd /c ver)[1] -like "\*6.1\*"
或
[Environment][Environment]::OSVersion.VersionString -like "\*6.1\*"

(systeminfo /FO CSV | ConvertFrom-Csv ).'os 名称' -like "\*7\*"
powershell 传教士 原创文章。始于 2015-12-15 允许转载，但必须保留名字和出处，否则追究法律责任
英文的系统用：
(systeminfo /FO CSV | ConvertFrom-Csv ).'os name' -like "\*7\*"
或用wmi方法：
(Get-WmiObject -Class Win32\_OperatingSystem).Caption -like "\*7\*"

判断win8，win8.1
(cmd /c ver)[1] -like "\*6.3\*"
或
[Environment][Environment]::OSVersion.VersionString -like "\*6.3\*"

(systeminfo /FO CSV | ConvertFrom-Csv ).'os 名称' -like "\*8\*"
英文的系统用：
(systeminfo /FO CSV | ConvertFrom-Csv ).'os name' -like "\*8\*"
或用wmi方法：
(Get-WmiObject -Class Win32\_OperatingSystem).Caption -like "\*8\*"

判断win10
(cmd /c ver)[1] -like "\*10.\*"
或
[Environment][Environment]::OSVersion.VersionString -like "\*10.\*"

(systeminfo /FO CSV | ConvertFrom-Csv ).'os 名称' -like "\*10\*"
英文的系统用：
(systeminfo /FO CSV | ConvertFrom-Csv ).'os name' -like "\*10\*"
或用wmi方法：
(Get-WmiObject -Class Win32\_OperatingSystem).Caption -like "\*10\*"

判断win2012 r2
(cmd /c ver)[1] -like "\*6.3\*"
或
[Environment][Environment]::OSVersion.VersionString -like "\*6.3\*"

(systeminfo /FO CSV | ConvertFrom-Csv ).'os 名称' -like "\*2012 r2\*"
英文的系统用：
(systeminfo /FO CSV | ConvertFrom-Csv ).'os name' -like "\*2012 r2\*"
或用wmi方法：
(Get-WmiObject -Class Win32\_OperatingSystem).Caption -like "\*2012 r2\*"

