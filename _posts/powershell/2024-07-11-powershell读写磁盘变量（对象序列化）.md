---
categories: powershell
layout: post
permalink: /powershell/powershell读写磁盘变量（对象序列化）
---

# ---【第一章，开篇引子】---

很久很久以前，我在网上闲逛，看到一头powershell菜鸟，写了一篇感慨道“挖哈哈，我学会powershell啦，我以前很笨，各种不懂，还想把变量保存起来。”

实际上我想说这并不算笨。

大多数变量，是我们需要处理的数据。但凡变量，都是我们需要用到的。都是在内存中的。 数据处理完成之后，99%都不需要了。 但是还有那么1%变量是我们下次还想再用的，这些个变量我们就需要保存起来。 比如游戏数据存盘，软件配置。

powershell 传教士 原创文章 2014-07-23，2015-02-27，2015-06-16，2016-06-23，2016-07-26修改，允许转载，但必须保留名字和出处，否则追究法律责任

ini xml txt 配置 配置文件 配置文档 序列化 反序列化 Import-Clixml Export-Clixml Import-Csv Export-Csv

# ---【第二章，powershell磁盘变量的用途】---

游戏大家都玩过，有些游戏很难，所以哦存盘文件大家都修改过。

里面存盘文件中，大都没有变量名=屎！

有些是台湾big5码的 =屎！

大都是十六进制的，还有高低位=屎！

在powershell中这一切都不存在了，下面向大家展示的是10进制的，带unicode变量名的，游戏存盘文档读写方法。

用途1：磁盘中的游戏存档

用途2：软件配置等

用途3：用于本机进程之间传值（传的是对象）

用途4：用于远程进程之间传值（传的是对象）

# ---【第三章，如何写入，读取普通变量】---

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$玩家名 = '传脚10'
$金子 = 100
$木头 = 200
$石头 = 300

$save =
@"
`$玩家名 = $玩家名
`$金子 = $金子
`$木头 = $木头
`$石头 = $石头
"@
out-file  a:\y游戏配置.ps1   -inputobject     $save    -Encoding   unicode
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

读入也很容易，在powershell中 【.   a:\\y游戏配置.ps1】即可，其实就是运行脚本。

# ---【第四章，如何写入，读取【哈希表】、【数组】等对象（即对象序列化）】---

本地powershell对象（或者.net对象）--->序列化成xml（或者json）--->传递文件（或者2进制流）到远程--->反序列化成对象--->在远程使用powershell对象（或者.net对象）

powershell对象（或者.net对象）--->ConvertTo-Json--->json

json--->ConvertFrom-Json--->powershell对象（或者.net对象）

写入：

```
$玩家 = @{ "红" = 100; "蓝" = 200 }
$玩家 | Export-Clixml -LiteralPath a:\y游戏配置.xml
```

读取：

```
$玩家 = import-Clixml -LiteralPath a:\y游戏配置.xml
$玩家
```

注意：每个对象对应一个xml文件。

# ---【第五章，表的序列化】---

行列整齐数据（表），的（反）序列化就用 Import-Csv Export-Csv 。

其中Import-Csv倒入后是一个数组。数组的每个元素，都有n个属性，对应csv的列名。

其中Export-Csv可以用-Append追加。-NoTypeInformation去掉表头。

# ---【第六章，还可以写入什么？】---

脚本中不但可以写入变量，用于数据存储。还可以写入代码，函数，命令对吧

# ---【第七章，补充一点，powershell读写变量，基本也不需要环境变量。】---

环境变量的本质是注册表。 环境变量的值，只能存放4095个英文字符。

环境变量随时可以写。写入的内容，只有在新建立cmd，powershell进程那一刻，才去读。

如某脚本每秒写入环境变量值。比如这一刻，变量值a=55。这时新建powershell进程，可以读到a=55。 但下一秒a=56了，但这个新建powershell进程中，环境变量并不更新。

结论，多进程计算中，用环境变量来交换公用数据，即读写环境变量，还不如直接读写注册表，并不比读写文件快，也不比读写文件方便。

# ---【第八章，结论】---

我们玩powershell的人，需要游戏存档，需要绿色软件，需要软件配置文件，但基本不需要读写注册表，基本不需要环境变量，基本不需要读写\*.txt，\*.ini，只需要\*.ps1111111  ！ 而且既然是ps1了，配置文档，就不光是文档了，还可以有段落，有函数，有命令。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [xml](https://www.cnblogs.com/piapia/tag/xml/) , [ini](https://www.cnblogs.com/piapia/tag/ini/) , [配置](https://www.cnblogs.com/piapia/tag/%E9%85%8D%E7%BD%AE/) , [序列化](https://www.cnblogs.com/piapia/tag/%E5%BA%8F%E5%88%97%E5%8C%96/) , [反序列化](https://www.cnblogs.com/piapia/tag/%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96/) , [Import-Clixml](https://www.cnblogs.com/piapia/tag/Import-Clixml/) , [Export-Clixml](https://www.cnblogs.com/piapia/tag/Export-Clixml/) , [Import-Csv](https://www.cnblogs.com/piapia/tag/Import-Csv/) , [Export-Csv](https://www.cnblogs.com/piapia/tag/Export-Csv/)
