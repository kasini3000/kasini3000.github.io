# ---【前言】---

2021-07-17 第一版

有些对比领域缺失，这篇文章会持续更新。

这是一篇对比powershell和nushell的文章。我是powerhsell专家，又是nushell新手，若某些观点有错误欢迎斧正。

为什么要对比powershell和nushell？因为它俩很像，不对比没天理！

# ---【正文】---

## ---【操作系统支持程度和安装】---

Nushell不支持win7/8.1,和对应的win2008，win2012，我觉得是个不小的败笔。目前nushell不支持arm，但这只不过是很小的问题。Nushell和powershell，都依赖少许linux系统包。Powershell有我做的一键安装脚本。https://www.cnblogs.com/piapia/p/13509081.html

现在认为，nushell和powershell安装容易程度差不多，都很容易。不分上下。

## ---【语法】---

Nushell中应该没有【对象】的概念。也就没有【属性】，【方法】概念。

但是却用内部命令，实现了【属性】： (ls).name。

内部命令没有实现【方法】。而是用另外的命令，实现的功能。但有这两两个弊端：1 需要通过管道使用。2方法有重载功能，管道后的命令，我不知道怎么传递参数。我猜要写过滤器，或接收器。

刚刚添加了for语法，还没有while语法。有if，貌似却没有else？没有switch case。没有return。特别依赖管道。

Powershell数组内每个元素是对象，对象类型可以不同。每种对象类型，有自己的属性方法。哈希表也是如此。而nushell的数组，哈希表中，没有对象，数组中无类型不同的值问题还不大，但哈希表，这种键值对，套在nushell table表中，如何表示，我还比较好奇。

脚本怎么传值，写参数是否方便，我还不知道。因为没有文档可以研究。

在win的nushell中，nushell的脚本名，补全失败。参数名估计也没法补全。

## ---【库，命令】---

Nutshell内置sqlite，bson，这两个库，是极好的。这块nushell赢了，赢在内置和简单。Bson是一种nosql数据库的格式，芒果db使用它。Powershell也很容易使用跨平台的sqlite和bson。搜 powershell bson，就可以搜到我很久前写的powershell操作bson的帖子。若使用powershell，推荐看我的帖子，并使用litedb，这本质是一个nuget上的dll。

传统的Shell人用txt，csv，很少有人用sql，nosql数据库，且貌似powershell领域也很少人用，真是悲Y剧啊。脚本应该大力推广sqlite和bson。那些用nushell的人，会去用sqlite，和bson吗？我暂且保持悲观态度。

## ---【对比nushell的ps，get-process】---

我们知道，跨平台或第三方命令，永远缺少特殊的参数，和命令。永远不如系统自带的。get-process ，和python的ps，nushell的ps都是这样。get-process对于win是完美的，对于linux有时候还需要用系统自带的ps。而nushell的ps，即不是win的ps，又不是linux的ps，有些特殊情况下，还得用系统自带的ps。对于用powershell使用linux的ps，问题不大。因为ps输出是字符串，就是字符串构造对象的问题。Nushell中用powershell的get-process，我用着是有问题的，我不会从powershell到nushell格式数据的转换，也就无法继而用 “| where size >10kb”之类的命令。在nushell中，你可以试试此命令：

powershell.exe -noprofile -c "get-process" | autoview

## ---【扩展性】---

nushell扩展性不如powershell，甚至可以说是完败。需要json-rpc协议。具体你得去看看nushell plugin开发规范。

而powershell方便很多：

1 已经有很多支持.net和.net core的dll，如mysql-data.dll。都可以被powershell加载使用。

2 nuget中的.net的dll。所有第三方人，开发的dll，都可以看做在nuget中。

3 powershell画廊中的第三方模块。

## ---【nushell的中文支持】---

Nutshell 变量名，函数名，支持中文，支持数字打头。nu脚本不支持bom头，只支持utf8编码。

## ---【nushell现有的问题】---

可以定义带有点的变量，却无法使用，什么鬼？如：let a.a = 1 ,用\$a.a  \${a.a} 不能输出。

在win的nushell中，的文件名补全。可以补全目录名（支持），目录中的文件名，有时候不能自动补全。文件名补全，打一个字母后，不能补全，这个应该是个bug。

## ---【结论】---

1 nushell对比powershell，目前类似于低Z仿lv，2---3年以后估计能成高D仿。

现有的功能的学习难度曲线和powershell差不多。

扩展性不如powershell，没有大厂家支持，没有大的项目（比如vmware用powershell，打造的虚拟机管理工具：powercli）。没有类，对象，属性，方法。

2nushell vs python：从运维的角度，nushell比python强。因为没有python的三个癌F症。Python对于运维的三个癌G症。参见 https://www.cnblogs.com/piapia/p/14962312.html

3敌手：

对于powershell用户来讲，Nushell是powershell最大的对手，或敌手。

对于shell用户（或脚本专家）来讲，nushell是最大敌手。

似乎可以把nushell看做powershell的徒弟。它俩把【老的饭醉团E伙bash-awk-sed】，打成了【烧脑劣功法】。5---10年 后甚至或许能代替bas。

4计算机语言的对比，应该着重于语法和库。其中库占80%，学习难度差不多，不宜扩展，为什么要用？

所以我目前的结论是不建议使用nushell。

5或许恨powershell，却又喜欢powershell特性的人，会学习并使用nushell。它俩都基于mit开源协议。但即便它再好，微软也不会用它。

6 nushell目前最大的问题，是库少，不完善。但我通篇并没有提到它。我相信随着时间的推移，这个问题会逐渐改善，库将来啥样，会不会变得真香？我说不准。

7powershell的强大之处有很多，如多线程，多进程，（含计时器，线程安全型变量）属性，方法，管道左右传递对象，远程本地powershell之间传递对象（自动序列化，反序列化），编写脚本参数很简单（带自动补全）。是值得nushell下一步山D寨（学习模仿）的地方。

8过几年，除非它能成为某发行版的默认shell。否则，公司招聘不会以nushell作为招A聘关键字。而powershell逐渐进入公司招C聘关键字。

9nushell的靓点。就是导入表格数据容易，这给了shell用户，使用awk之外的另一个选择。会吸引到shell用户来使用吗？观望中。。。

## ---【讨论】---

我认为，这些年powershell对象脚本的概念，用法，和方便之处，已经随着win10的普及深入人心。并且打A得bash-awk-sed挺惨。Powershell和nushell都是采用mit协议的。5----10年后，会有linux发行版，让他俩上位吗？

还是linux的shell永远是bash？

# ---【后记】---

这只是简单对比，欢迎就这两个shell的对比，提出你感兴趣的场景，让我完善这个对比。谢谢观看。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [对比](https://www.cnblogs.com/piapia/tag/%E5%AF%B9%E6%AF%94/) , [nushell](https://www.cnblogs.com/piapia/tag/nushell/) , [脚本](https://www.cnblogs.com/piapia/tag/%E8%84%9A%E6%9C%AC/) , [shell](https://www.cnblogs.com/piapia/tag/shell/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)
