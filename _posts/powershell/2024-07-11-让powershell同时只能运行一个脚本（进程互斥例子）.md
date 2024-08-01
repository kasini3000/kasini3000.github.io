---
categories: powershell
layout: post
permalink: /powershell/让powershell同时只能运行一个脚本（进程互斥例子）
---
powershell,mutex,互斥,进程互斥,脚本互斥

powershell脚本互斥例子，在powershell类别文章中，声明原创唯一。

powershell 传教士 原创文章 2016-07-02 ,2017-10-10 更新。允许转载，但必须保留名字和出处，否则追究法律责任

# ---【前言】---

```
    有时候我们希望脚本并发，用多进程、多线程尽快地跑。也有时我们希望同时只能运行一个脚本。
```

linux中bash的做法是，脚本先检测特定文件，没有就建立这个文件，并运行。运行完后就删除。 这种方法对于powershell来说已经out了，老奶奶了。这个方法有两个弊端：

1 是慢，内存速度是磁盘文件的1000左右倍。

2 如果脚本非正常终止，没有删除特定文件，那么下一次脚本将无法运行。

而。net（powershell）给我们提供了【内存中的】特定值，就是用System.Threading.Mutex类，来让powershell脚本享受二进制程序相同的待遇。

当然，我感觉还是有点不完美，即这个值是基于进程，而不是基于脚本。

# ---【脚本调用时的注意事项】---

```
    正由于这个弊端，所以我们要在cmd中，这样调用脚本：
```

powershell -file mutex\_powershell进程互斥例子.ps1

即单开一个进程然后再调用，在powershell中调用脚本也是这样。 当然也可以用start-process调用。就是不能直接调用。

假设你直接调用：

开两个powershell进程。在powershell\_a中运行这个脚本，把powershell\_a进程注册成了唯一。 再在powershell\_b中再运行这个脚本，将永远显示“互斥失败”。那是因为,即使powershell\_a中的脚本运行结束了，但powershell\_a这个进程还没结束。

# ---【猪懂傻改 脚本例子】---

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
# PowerShell 进程（脚本）互斥的例子
$互斥名字 = 'Global\我代号为天王盖地虎'
$建立互斥成功否 = $flase
$互斥对象 = New-Object System.Threading.Mutex ($true,$互斥名字,[ref]$建立互斥成功否)
if ($建立互斥成功否)
{
    write-host '互斥成功，开始干活!'
    start-sleep  -Seconds 60 #你的任务
    $互斥对象.ReleaseMutex() | Out-Null
    $互斥对象.Dispose() | Out-Null
    write-host '活干完了，释放'
}
else
{
# 每个互斥脚本必须单独占用一个进程！powershell传教士 win7 ,win10, powershell core v6.0 beta8 on linux测试通过
    write-host '互斥失败 !'
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

谢谢看帖，完。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [mutex](https://www.cnblogs.com/piapia/tag/mutex/) , [互斥](https://www.cnblogs.com/piapia/tag/%E4%BA%92%E6%96%A5/) , [进程互斥](https://www.cnblogs.com/piapia/tag/%E8%BF%9B%E7%A8%8B%E4%BA%92%E6%96%A5/) , [脚本互斥](https://www.cnblogs.com/piapia/tag/%E8%84%9A%E6%9C%AC%E4%BA%92%E6%96%A5/)
