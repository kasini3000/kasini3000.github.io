---
categories: powershell
layout: post
permalink: /powershell/用powershell+excel行列转置三步走
---

本文重点讲解第一步，手动在excel表中输入公式，或者用powershell自动输入公式。

第二步，用powershell向excel中写入数据，略。

第三步，用powershell从excel中读取数据，略。

本人原创拙作，声明网上唯一。

搜索过的关键字：powershell excel 行列 转置 转换

搜索过的引擎：百度 bing

powershell 传教士 原创文章 2014-07-12，2015-03-03，2016-07-17 修改。 允许转载，但必须保留名字和出处，否则追究法律责任

# ---【第一章 概述】---

```
用excel + powershell来数据处理，太方便。 大型数据处理当然要用几种主流的数据库了，这不用多说。对于差不多少于一亿条的【中小型】数据处理， 建议用excel+powershell来处理。优点如下：
```

1  excel是c代码的编译程序，性能没问题，要比powershell处理得快。64位win的话，用64位excel就更好了。

2  excel简单，会的人多。

3  excel可以输出各种统计图，表，打印。

4  每个excel单元格，可以看作是一个变量。首先设置单元格的格式，也就是变量类型是数值啊还是日期等。然后在变量中存东西。

5  每个excel公式可以看成是表达式，或者叫做存储过程。

6  每个excel文件（工作簿），可以认为是一个数据库。每个工作表可以看作是数据库中的表，表中含有的行是记录，列是字段。

7  只用powershell做存取，不做任何运算，运算都丢给excel公式！。用excel公式写具体的数据处理算法，如第三个单元格中的内容等于第一个单元格加第二个单元格。

8  excel实际上是可以连接数据源，也就是从数据库中存取常用的数据类型。

# ---【第二章 人+键盘+鼠标+excel 手动实现行列转置 教程】---

```
从excel2007开始， excel 支持的最大行数 = 1048576         excel 支持的最大列数 = 16384    所以excel行列转置最多支持16384 x 16384 这样方阵的转置。
```

首先讲解如何手动操作，设置转置：

1打开excel，在sheet1中，做好一个表，如图1

![](https://images2015.cnblogs.com/blog/456691/201607/456691-20160719151548357-183726961.png)

2在sheet2中，选中第一个格，输入公式=TRANSPOSE(Sheet1!A1:D11)，然后选中a1---d11,按f2，按ctrl+shift+enter

3修改第一个工作本中的内容，第二个工作本也变了，证明成功。

![](https://images2015.cnblogs.com/blog/456691/201607/456691-20160719151629544-1899265529.png)

一般来讲，建议按照上述教程，用键盘鼠标，手动输入公式。而不是powershell输入公式。而powershell只做数据读写。

# ---【第三章  用powershell向sheet1写入数据 （略）】---

# ---【第四章  用powershell向sheet2写入公式】---

一般来讲，建议按照上述教程，用键盘鼠标，手动输入公式。而不是powershell输入excel公式。而powershell只做数据读写。当然如果你非要用excel输入公式，那么请看：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
# powershell 行列转置例子

<#

行列转置公式为：
=TRANSPOSE(Sheet1!A1:XFD16384)
{=TRANSPOSE(Sheet1!A1:CV100)}
{=TRANSPOSE(Sheet1!A1:KUR8000)}

#>

Write-Warning  '支持最大行列为 16384,行列可以不是正方形，但建议在6000 * 6000条以下，大内存可以8000 * 8000'
$script:脚本存储路径 = Split-Path -Parent   $myinvocation.mycommand.path
$Env:Path += ";$script:脚本存储路径;"

#region main
$excel文件 = 'a:\pscode\temp122\z转置测试002.xlsx'
$excel对象 = New-Object -comobject Excel.Application
$SheetName = "sheet2"
$Excel工作簿 = $excel对象.Workbooks.Open($excel文件)
$Excel工作表 = $Excel工作簿.Worksheets.Item($SheetName)
$excel对象.Visible = $true

#region  向sheet2写入公式
# 一般来讲，建议按照上述教程，用键盘鼠标，手动输入公式。而不是powershell输入公式。而powershell只做数据读写。
# 没找到直接写入公式的方法，暂时先用笨方法。
[system.reflection.assembly]::loadwithpartialname("System.Windows.Forms") | Out-Null
$范围1 = $Excel工作表.range("A1:CV100")
[string]$temp0001 = '=TRANSPOSE{(}Sheet1!A1:CV100{)}'

$范围1.select()

#此为 powershell 传教士 原创文章 中配套的脚本 2014-07-12

#公式栏
sleep 1
[System.Windows.Forms.SendKeys]::SendWait("{F3}") | Out-Null

#粘贴
sleep 1
[System.Windows.Forms.SendKeys]::SendWait($temp0001) | Out-Null
sleep 1
[System.Windows.Forms.SendKeys]::SendWait("^+{ENTER}") | Out-Null

# 重新计算公式
sleep 1
[System.Windows.Forms.SendKeys]::SendWait("+{F9}") | Out-Null

sleep 1
$excel对象.Visible = $false

#powershell  行列 转置  转换  excel  powershell传教士  2014-07-12
sleep 2
$excel对象.save()
sleep 2
#endregion  向sheet2写入公式



#region 向sheet1写入行列数据
# 略，注意，写完保存
#endregion 向sheet1写入行列数据



#region  从sheet2读取值
# 略，注意，过滤 “0”值
#endregion  从sheet2读取值


#region  扫尾
Start-Sleep 2
$Excel工作簿.close()
Remove-Item  "$home\documents\resume.xlw" -ErrorAction:SilentlyContinue
Start-Sleep 2
$excel对象.save()
sleep 2
$excel对象.Quit()


Start-Sleep 2
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($范围1)
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($Excel工作表)
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($Excel工作簿)
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($excel对象)
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($excel对象)
Start-Sleep 2
[System.GC]::Collect()
[System.GC]::WaitForPendingFinalizers()

Remove-Item  "$home\documents\resume.xlw" -ErrorAction:SilentlyContinue
Write-Host "------------end-------------"
Write-Host "---脚本运行完毕后，请输入exit命令。或，关闭powershell窗口，后台excel.exe即可自动退出。---"
exit 0
#endregion  扫尾

#endregion main
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# ---【第五章  用powershell从sheet2读取数据 （略）】---

# ---【第六章  值的后续处理】---

用powershell从excel中读取，得到m行n列的数据后，可以：

输入，输出xml文件-------------powershell就有Select-Xml，ConvertTo-Xml相关处理命令。

输入，输出json-------------ConvertFrom-Json，ConvertTo-Json

输出html-------------ConvertTo-Html

输入，输出csv--------------ConvertFrom-Csv，ConvertTo-Csv，Export-Csv，Import-Csv

# ---【后记】---

使用【原厂】or【第三方】库之辩：

任何语言处理excel，要么调用原厂的库（即excel com对象），要么调用第三方的库。

第三方插件的好处是【分离】。无需装office。但明显有兼容性问题。这些第三方excel库，有些免费，有些也收费。

而office原厂excel com 插件，需要安装office，有收费问题。但是自家东西100%兼容。

问：有哪些语言可以处理excel？

答： 我所知道的

java + 第三方模块

c# + 第三方模块

c# + excel的com对象

vbs + excel的com对象

powershell + excel的com对象

powershell + 第三方模块

python + 第三方模块

使用【程序语言】or【sql】，哪种方法处理我excel中的数据更好？之辩：

powershell+excel处理数据的本质就是。用脚本语言+【数据处理逻辑】搬箱子（excel单元格）。 这种搬箱子是必要的，比把txt，csv导入库，然后用sql处理好太多，理由如下：

1 因为数据库的列类型是锁定的。而且sql在字段之间搬数据不方便。excel没有这类问题。

2 关系型数据库中没有【第几行】这个概念。

3 关系型数据库是严格的行列表，而excel中有【m行合并的单元格】，【n列合并的单元格】。

4 excel表中的数据，有可能不是很规范，需要处理后才是严格的行列数据，才能入库。

-完-

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [excel](https://www.cnblogs.com/piapia/tag/excel/) , [行列](https://www.cnblogs.com/piapia/tag/%E8%A1%8C%E5%88%97/) , [转置](https://www.cnblogs.com/piapia/tag/%E8%BD%AC%E7%BD%AE/) , [转换](https://www.cnblogs.com/piapia/tag/%E8%BD%AC%E6%8D%A2/)
