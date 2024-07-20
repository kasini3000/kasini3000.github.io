# 中文名： 《电壳别名宝》

# English name： 《Power Alias》

# powershell 编写的tui界面脚本。

# 用途：保存容易记住的别名（支持中文），保存linux命令。并用powershell执行它。主治：记不住linux命令。

# 界面截图：

![](https://img2020.cnblogs.com/blog/456691/202110/456691-20211013174548435-602360785.gif)

# 下载：

git clone git@gitee.com:chuanjiao10/powershell.git

# 网址：

[https://gitee.com/chuanjiao10/powershell/tree/master/win-linux通用/poweralias](https://gitee.com/chuanjiao10/powershell/tree/master/win-linux%E9%80%9A%E7%94%A8/poweralias)

# 使用说明：

1 一键安装linux版powershell 。 [https://gitee.com/chuanjiao10/kasini3000\_agent\_linux](https://gitee.com/chuanjiao10/kasini3000_agent_linux)

2 复制这些文件，到linux磁盘的目录上：

NStack.dll
p11.ps1
Terminal.Gui.dll

3 运行终端软件，如putty。

4 编辑ssh连接，设定终端类型为：【putty-256color】 。（注意： 不要用xterm）

5 使用终端软件，运行ssh连接。（注意： 必须用终端软件，不支持win terminal）

6 在终端中运行：pwsh。

7 p11.ps1

# 多个别名菜单：

1 复制 p11.ps1 成为p12.ps1

2 编辑 p12.ps1 ，把 '\~/.power\_alias11.xml' ，改成 '\~/.power\_alias12.xml'

3保存。

# 其他：

* 脚本使用 powershell 调用 【Terminal.Gui】库开发。
* 脚本win，linux通用。不过win上有点小问题。

因为win上可以用powershell调用winform写图形程序。所以我认为win上没人去用tui。

powershell tui脚本主要是给linux脚本人用的。

比如写个选择choice并返回值的脚本；

比如配置网卡ip的脚本等。

powershell tui程序，本质win，linux通用。支持powershell v5.x v7.x

# 能否掀起powershell tui图形界面脚本开发的潮流？我想要引领 ；）

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [linux](https://www.cnblogs.com/piapia/tag/linux/) , [alias](https://www.cnblogs.com/piapia/tag/alias/) , [别名](https://www.cnblogs.com/piapia/tag/%E5%88%AB%E5%90%8D/) , [tui](https://www.cnblogs.com/piapia/tag/tui/)


