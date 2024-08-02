---
categories: powershell
layout: post
permalink: /powershell/powershell递归算法的例子
---

递归，简单地说就是函数不停地调用自身。循环和递归，是程序常用解题方式，几乎99%的语言都有。网上用powershell递归法解题的脚本倒是有，但都比较长，不容易理解。而讲powershell递归算法的简单例子貌似没有

 递归，简单地说就是函数不停地调用自身。 循环和递归，是程序常用解题方式，几乎99%的语言都有。 递归使写程序更简单，更清晰，但比循环更占内存。

        那么如何从递归中退出呢？一般建议把递归放在if中，或else中。也就是说在递归外面判断条件。条件成立，或者不成立，再无限递归。

        不过貌似脚本语言递归的性能都不太好，因为库都比较大，出栈入栈就好像胖子过通道。 而powershell的递归在脚本语言中还算是差的，因为年头毕竟短，而且并没专门考虑过优化。 当然这都是传言，我的简单试验，性能还可以，大家也多用用递归，多测测啊。



复制代码
function 用递归法计算阶乘($值)
{
    if($值 -gt 1)
    {
        return ( $值 * (用递归法计算阶乘  ($值-1))  )
    }
    else
    {
        return $值
    }
}

#递归结果超过double类型的最大值时，就要用下面的【无穷大正整数】，但注意：安装 .net4 先！
[reflection.assembly]::loadwithpartialname("System.Numerics")
$anser_first="1005"
$大数=[System.Numerics.BigInteger]::Parse($anser_first)

Measure-Command
{
    Write-Host  (用递归法计算阶乘 $大数)
}
复制代码


----------------------------测试结果----------------------------

2013年测试成绩，算1005位用了2秒多。

Ticks             : 26510676

Days              : 0

Hours             : 0

Milliseconds      : 651

Minutes           : 0

Seconds           : 2

TotalDays         : 3.06836527777778E-05

TotalHours        : 0.000736407666666667

TotalMilliseconds : 2651.0676

TotalMinutes      : 0.04418446

TotalSeconds      : 2.6510676

----------------------------

2017年测试成绩，算1005位用了半秒多。

Ticks             : 5438699

Days              : 0

Hours             : 0

Milliseconds      : 543

Minutes           : 0

Seconds           : 0

TotalDays         : 6.29479050925926E-06

TotalHours        : 0.000151074972222222

TotalMilliseconds : 543.8699

TotalMinutes      : 0.00906449833333333

TotalSeconds      : 0.5438699

 ----------------------------

2022年用 powershell 7.3 preview 5 测试 230毫秒 （基于.net 7）

计算速度越来越快了！

哦 17年，22年用的是同一台机子。13年用的啥机子忘了。



powershell 递归 算法 例子 powershell  灰主牛 官网 官方网址 电子书  阅读器  语音计算器 efs copy backup ps_ping ps_tracert 三贱客 天罡茅山秘
