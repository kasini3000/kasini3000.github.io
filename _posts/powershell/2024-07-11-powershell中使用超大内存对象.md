---
categories: powershell
layout: post
permalink: /powershell/powershell中使用超大内存对象
---

简单介绍了powershell中超大内存对象的用途，开启powershell超大内存对象的办法。

powershell 传教士 原创文章 2016-12-31 允许转载，但必须保留名字和出处，否则追究法律责任

powershell 2GB large object .net 大对象

# 前 言

问：powershell中，哪些对象有可能会占用大内存？

答： stringbuilder对象，大数组arraylist，hashset等。

问：powershell中，stringbuildr对象有什么用？

答： 还是比较常用的，让大文件在内存中查找替换，这样比string快不少。

问：1GB的stringbuilder对象，能存多少文本？

答： .net 采用utf16，即不论一个英文字母还是一个汉字，都占用2个字节。 所以说1GB的文本文件，读取入内存后，大概要占用2.1GB左右的内存。

# 正 文

问题的提出： 一个变量占用1GB内存，算是大么？

我有台测试机子，用的是win10 ltsb 2016的系统，物理内存还剩30G，虚拟内存20G。 但是建立个1GB的对象都报错。而建立900MB的就不报错了，代码如下：

```
[System.Reflection.Assembly]::loadwithpartialname("System.Text") | Out-Null
[int32]$long = 1gb
$内存字符串对象 = New-Object System.Text.StringBuilder($long)
```

本来手册中说，默认能使用2GB内存的。唉，这年头啥都缩水呀，看来我只能启用.net的超大对象了。

前提：

1 用64位系统，物理内存足够，虚拟内存足够。

2 .net版本至少是4.51，当然最好是最新版4.62。

3具体解决办法：

用记事本，新建一个文本文件，

文件名为：【powershell.exe.config】

文件编码为：【utf8+bom头】，即用记事本保存成utf8编码。

文件内容为：

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <runtime>
        <gcAllowVeryLargeObjects enabled="true" />
    </runtime>
</configuration>
```

保存后，粘贴此文件到【c:\\Windows\\System32\\WindowsPowerShell\\v1.0】

再重新运行powershell.exe，重新运行上述脚本代码，就不报错了。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [2GB](https://www.cnblogs.com/piapia/tag/2GB/) , [large](https://www.cnblogs.com/piapia/tag/large/) , [object](https://www.cnblogs.com/piapia/tag/object/) , [.net](https://www.cnblogs.com/piapia/tag/.net/) , [大对象](https://www.cnblogs.com/piapia/tag/%E5%A4%A7%E5%AF%B9%E8%B1%A1/)
