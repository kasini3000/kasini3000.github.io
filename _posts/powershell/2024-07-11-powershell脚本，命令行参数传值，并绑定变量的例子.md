---
categories: powershell
layout: post
permalink: /powershell/powershell脚本，命令行参数传值，并绑定变量的例子
---

前人写了几篇powershell传值的帖子，但那些都是讲$args的。而我这篇主要讲CmdletBinding的。这是小技巧文章，所以文章不长。但原创唯一，非常重要。

这是小技巧文章，所以文章不长。但原创唯一，非常重要。我搜了下，还真没有人发 powershell怎样 【命令行 参数 绑定】，所以我决定写成博客。

搜索关键字如下：

powershell 命令行 参数 绑定

powershell 传入 参数

powershell 传递 参数

powershell CmdletBinding

powershell 命令行 参数 绑定 传入 传递 parameter CmdletBinding

powershell 传教士 原创文章。始于 2016-09-26 允许转载，但必须保留名字和出处，否则追究法律责任

# ---【写文章初衷】---

powershell 前人写了几篇powershell传值的帖子，但那些都是讲\$args的。而我这篇主要讲CmdletBinding的。大家不可不知。

# ---【powershell 脚本的 命令行参数绑定】---

问：什么是\$args传值法？

答：我把它称为【参数位置传值法】。 \$args 是一个对象数组。数组中的值，分别为 【第一个参数】，【第二个参数】，【第三个参数】

设命令行为：

ps>a脚本1.ps1 参数1 参数2 参数3

那么 \$args[0] 的值为【参数1】， \$args[1] 的值为【参数2】， \$args[2] 的值为【参数3】，

我个人喜欢这种传值方法。因为写起来用起来简单。 但是有个缺点，位置固定。

问：什么是CmdletBinding传值法？

答：我把它称为【给脚本内部变量名】传值法。 这种传值法适合于传递多个参数值，并且参数值的位置随机。

设命令行为：

ps>b脚本2.ps1 -嫦娥姐姐刚走\_国庆节就要来了\_十一吃点啥好呢 “攒钱等双十一剁手中”  -中国的英文单词是什么 ‘china’

那么脚本应该这样写：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
[CmdletBinding()]
Param(
    [string]$嫦娥姐姐刚走_国庆节就要来了_十一吃点啥好呢,
    #脚本命令行参数绑定例子 powershell传教士 制作 分享
    [string]$中国的英文单词是什么,
    [string]$aAaa = 'k',
    [Int32]$数字 = 17
)

$a = 'xxx' + $嫦娥姐姐刚走_国庆节就要来了_十一吃点啥好呢 + 'yyy'
write-host $a

write-host $中国的英文单词是什么
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# ---【powershell 函数 的命令行绑定】---

函数的命令行绑定，也是一样，请看代码：a.psm1

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
function aaa
{
    [CmdletBinding()]
    Param(
        [string]$今天好心情_老熊请吃,
        #脚本命令行参数绑定例子 powershell传教士 制作 分享
        [int32]$a = 123
    )

    write-host "老熊今天请吃 ？ $今天好心情_老熊请吃 ！！！"
    Write-Host $a
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

a.psm1使用方法：

import-module d:\\xxx\\a.psm1

aaa      -今天好心情\_老熊请吃        'ji'

返回：

老熊今天请吃 ？ ji ！！！

123

# ---【脚本的知识点】---

1 函数必须在Param前面加上一行 “ [CmdletBinding()] ”,才能用【变量名=参数名 传值法】。脚本不用这么做。

2 powershell命令行 支持：单引号，双引号，中文的单引号，中文的双引号。（linux版的powershell也完全支持这些）

3 powershell脚本 支持：中文变量名，中文参数名。（linux版的powershell也完全支持这些）

4 tab补全：

4.1 输入b，然后打【tab键】，即可补全出【b脚本2.ps1】。

4.2 出现【b脚本2.ps1】后，输入【空格】，然后打【-】，然后打【tab键】，即可补全出【嫦娥姐姐刚走\_国庆节就要来了\_十一吃点啥好呢】。当然英文也能补全出来。

5 【变量名=参数名 传值法】，【\$args参数位置传值法】。这两种传值方法，支持从powershell 2.0到最新版 powershell 5.1

参数名传值法，\$args传值法，可以同时使用。\$args中保存着，参数名传值法，不匹配的【参数名】【参数值】

-完-

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [命令行](https://www.cnblogs.com/piapia/tag/%E5%91%BD%E4%BB%A4%E8%A1%8C/) , [参数](https://www.cnblogs.com/piapia/tag/%E5%8F%82%E6%95%B0/) , [绑定](https://www.cnblogs.com/piapia/tag/%E7%BB%91%E5%AE%9A/) , [传入](https://www.cnblogs.com/piapia/tag/%E4%BC%A0%E5%85%A5/) , [传递](https://www.cnblogs.com/piapia/tag/%E4%BC%A0%E9%80%92/) , [parameter](https://www.cnblogs.com/piapia/tag/parameter/) , [CmdletBinding](https://www.cnblogs.com/piapia/tag/CmdletBinding/)
