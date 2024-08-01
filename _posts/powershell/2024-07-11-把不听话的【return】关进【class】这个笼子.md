---
categories: powershell
layout: post
permalink: /powershell/把不听话的【return】关进【class】这个笼子
---

问：powershell 最垃圾的语句是什么？
答：（函数中的）return

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子1：函数中的return有时会返回所有内容，而不是你想要的内容，如：
function aaa
{
    return 'bbb'
}

function ccc
{
    aaa
    return 'ddd'
}

ccc     #ccc函数不光返回了我们想要的值ddd，还把aaa函数的返回值bbb返回了。
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# -----------------我们心目中return的作用--------------------

我们用return想实现2个功能：
1 只返回1---n 个，我们想要的结果。
2 结束当前函数。-----这个功能可以用空的return来实现。
总之，不建议在函数中，用return后面加变量返回内容。

---------------解决方案1---------------

函数中有return，函数中又用了.net方法的情况。请在.net方法前加[void]，这样就不会带出多余数据。

powershell 传教士 原创文章 2014-07-18 2014-10-10 2015-03-22 2016-02-27更新，允许转载，但必须保留名字和出处，否则追究法律责任

# ---------------解决方案2 貌似不灵---------------

使用

return ,\$我想要的返回值
或
return @(,\$我想要的返回值)

---------------解决方案3 在函数外面，用 -is [array]判断返回值是单一，还是多个。推荐 ---------------

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子2：
if  ($函数名  -is [array])
{
    $我想要的返回值 = $函数名[-1]
}
else
{
    $我想要的返回值 = $函数名
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

---解决方案3变种，既然返回值或许是数组，那么就可以取第一个，或最后一个值---

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子3：有个缺点，必须明确知道返回值个数才行。
function  函数a
{
    $a = 1
    $b = 2
    $c = 3
    return $a,$b,$c
}
$d,$e,$f = 函数a  #函数返回的值，我全要

$g,$null,$null = 函数a  #我只想要函数返回的第一个值
$null,$null,$h = 函数a  #我只想要函数返回的最后一个值
write-host $d,$e,$f,$g,$h
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# ---------------解决方案4 diy，直接写入外部变量---------------

不用return，而用自定义的return2函数，和自定义变量\$global:return2保存要返回的内容。

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子4：直写外部变量
function  return2($youwant)
{
    $global:return2 = $youwant
}

function aaa
{
    return 'aaa'
}

function bbb
{
    aaa
    return2 'bbb'    #把返回值写入,或者直接写变量$global:return2 = 'bbb'
    return
    write-host   "运行不到这里"
}

write-host   $global:return2  #输出
$ccc = $global:return2    #读取
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

---

推荐

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子5：直写外部变量变种，用ref给函数传入【输出外部变量名】的内存地址。
function 函数_把输入的值加一($输入变量名,[ref]$输出变量名2)
{
    $输出变量名2.Value = $输入变量名 + 1
}

$输出变量名的内存地址 = $null                         #这行必须有
函数_把输入的值加一   8  ([ref]$输出变量名的内存地址)
$输出变量名的内存地址               #这里输出 9
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

---

结论： （函数中的return有坏的特性）
凡是函数中，又调用其他函数，其他函数也有返回值；
凡是函数中有\$aaa这样，把值打印到屏幕的；
凡是函数中有write-output \$aaa打印屏幕的；
用了return 都会有问题。

# ---------------解决方案5 把不听话的【return】关进这个【class】笼子 推荐 ---------------

ps 5.0 类的关键字：
hidden 不想被别人用的隐藏属性
static 静态关键字
\$this 本类的对象

powershell5.0 中的 class 类的研究：
1 类中不能 function xxx {}这样定义函数 。只能定义方法。
2 类中不能调用ps命令，不能调用ps函数。只能定义方法，并在方法中用命令，然后用return 返回数据。
3 类中可以用\$a=5，但不能用\$a=5;\$a。
4 类中可以用\$a=[aaa]::bbb()，但不能用[aaa]::bbb() 。
5 函数中可以定义类。
6 类名，类的属性名，类的方法名，都可以是中文。
7 类如果不用new新建，就不能调用其中的方法，除非方法带有 static （静态方法）前缀。类如果不用new新建，就不能使用其中的属性，除属性带有 static （静态变量）前缀。

类引发的好处：
由上述2可知，你不能在类的方法中用write-host，write-output等命令，【直接！】输出数据。
由上述3，4可知，类的方法不会【直接输出值！】，即不会返回【不想要的多余】数据。
类中没有函数中return的坏特性。

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#例子6：把return关入class，编写不会返回多余数据的脚本。例子6，实际上是 例子1 的class化改写。
function aaa
{
    return 'bbb'
}

class ccc
{
    static [string]ddd()
    {
        aaa
        return 'ddd'
    }
}

[ccc]::ddd()
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

结论：
不想要多余的数据？用class改写你的function。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [powershell传教士](https://www.cnblogs.com/piapia/tag/powershell%E4%BC%A0%E6%95%99%E5%A3%AB/) , [function](https://www.cnblogs.com/piapia/tag/function/) , [class](https://www.cnblogs.com/piapia/tag/class/) , [classes](https://www.cnblogs.com/piapia/tag/classes/) , [return](https://www.cnblogs.com/piapia/tag/return/) , [5.0](https://www.cnblogs.com/piapia/tag/5.0/)
