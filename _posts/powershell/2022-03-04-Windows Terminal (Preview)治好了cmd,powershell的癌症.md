




# Windows Terminal (Preview)治好了cmd,powershell的癌症



# 前言

话说n年前，我想开发一款powershell麻将游戏，但是发现命令行下无法显示麻将牌这种特殊符号。

经过研究发现，这是4字节的utf16le字符串。而powershell依赖的渲染引擎，只能渲染双字节的，即65535之内的。

Linux下的powershell没有这个问题。

我给powershell提交了issue，但是没有得到反馈。

随着时间的推移，随着git的普及，unicode字符渲染问题越来越严重了，因为git输出中有很多特殊字符串，用户呼声越来越高，才引起了ms的重视。也就有了Windows Terminal。

# 命令行渲染引擎

命令行渲染引擎是啥玩意？我以前不知道，现在我知道了。就是conhost.exe。

简单来讲老版本的conhost.exe，只渲染双字节=65535之内的utf16le，4字节的不渲染。

新版本的，对于4字节的扩展字符，现在也渲染了。

具体可以看看Windows Terminal原理介绍。

这是一个重大+基础的东西，但我不明白为什么微软不给旧的系统使用。

要求系统最低版本为：201905月版win10。Win10-18362

在我眼中，这就是在win中新建个conhost2.exe，然后让cmd.exe加参数调用它的问题。

或者新建 conhost2cmd.exe  ,,, conhost2powershell.exe。

# 图示：

图1

![](https://img2018.cnblogs.com/blog/456691/201908/456691-20190814124043517-732230713.png)

图2

![](https://img2018.cnblogs.com/blog/456691/201908/456691-20190814124104481-839360999.png)

图3

![](https://img2018.cnblogs.com/blog/456691/201908/456691-20190814124128650-25712964.png)

# 结论：

1使用这个新的conhost.exe，或者说使用Windows Terminal (Preview)，最大的好处，就是给cmd.exe,powershell.exe,pwsh.exe带来了显示特殊字符。

2 不要直接运行cmd.exe,powershell.exe,pwsh.exe，要使用套在Windows Terminal 壳子中的cmd.exe,powershell.exe,pwsh.exe。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [Windows Terminal](https://www.cnblogs.com/piapia/tag/Windows%20Terminal/) , [cmd](https://www.cnblogs.com/piapia/tag/cmd/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)










