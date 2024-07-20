关键字 linux powershell pwsh 补全 complete bash zsh

摘要：
linux用户的福音！
在linux版powershell中，补全linux外部命令的参数（如ls补全-l），支持bash和zsh。

# 概述

在linux版powershell中，默认是无法补全linux外部命令的参数的。
但是有了这个模块，就可以实现，用tab补全【外部命令的参数名】了。
支持常用linux命令，如ls，find，grep等。
支持bash和zsh。

# 项目官网：

https://github.com/PowerShell/Modules/tree/master/Modules/Microsoft.PowerShell.UnixCompleters

# 界面gif动图：

![](https://img2020.cnblogs.com/blog/456691/202111/456691-20211119171023753-1812244180.gif)

# linux中，的powershell中，安装：

Install-Module -Name Microsoft.PowerShell.UnixCompleters

linux中使用：

把下两条命令写入\$profile，否则每次使用前需要打入此2条命令。

```
import-Module -Name Microsoft.PowerShell.UnixCompleters
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
```

同时启用psreadline下拉列表，和linux外部命令tab补全后，\$profile的样子：
【文件名=/root/.config/powershell/Microsoft.PowerShell\_profile.ps1】

```
Set-PSReadlineOption -EditMode Windows
Set-PSReadlineOption -PredictionSource HistoryAndPlugin
Set-PSReadLineOption -PredictionViewStyle ListView
import-Module -Name Microsoft.PowerShell.UnixCompleters
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
```

注意：

此时，应该先
1列出所有旧版psreadline：get-module psreadline -ListAvailable
2删除所有旧版的psreadline：rm -rf /xxx/psreadline
3用下列命令安装psreadline最新版：
Install-Module -Name PSReadLine -AllowPrerelease -Force -Scope CurrentUser

# psreadline下拉菜单的gif效果：（支持linux）

![](https://img2020.cnblogs.com/blog/456691/202111/456691-20211119172424377-382540816.gif)

# 此功能面向的用户群：

我想这个功能的主要用户，是win，linux都用的用户，如wsl用户。
或者是，从win中，用win terminal（或powershell），经ssh，连接linux。
本地powershell7---》ssh---》远程powershell7，还想用linux外部命令，参数名的tab补全。

那么问题来了，或许有人会问？为什么不用？
本地powershell7---》ssh---》被控机bash，bash里也有complete呀！
答：本地远程都是powershell7的话，可以从本地传递对象到远程，可以从远程返回对象到本地。

谢谢观看

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [bash](https://www.cnblogs.com/piapia/tag/bash/) , [linux](https://www.cnblogs.com/piapia/tag/linux/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [pwsh](https://www.cnblogs.com/piapia/tag/pwsh/) , [补全](https://www.cnblogs.com/piapia/tag/%E8%A1%A5%E5%85%A8/) , [complete](https://www.cnblogs.com/piapia/tag/complete/) , [zsh](https://www.cnblogs.com/piapia/tag/zsh/)
