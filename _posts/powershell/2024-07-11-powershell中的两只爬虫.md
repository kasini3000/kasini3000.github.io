---
categories: powershell
layout: post
permalink: /powershell/powershell中的两只爬虫
---

# --------------------序--------------------

（PowerShell中的）两只爬虫，两只爬虫，跑地快，爬网页不赖\~\~\~ 一只基于com版的ie，一只基于.net中的WebRequest类，都是老奶奶，不奇怪 。。。 虽然很老了，但爬的也很快 。。。比python简单。。。

powershell 爬虫 spider Invoke-RestMethod Invoke-WebRequest

# --------------------概述--------------------

抓取（爬取）网上信息的脚本程序，俗称网络蜘蛛，又名爬虫。

用vbs或powershell调用ie浏览器的com对象，来解析html成为xml对象，从而扣取数据，是一种很老的爬虫办法，也很不错。 这里不多介绍，有感兴趣的去搜些vbs，powershell脚本即可。

用vbs或powershell调用.net中的WebRequest类，来解析html成为xml对象，从而扣取数据，是另一种挺好的办法。

如果你的系统是win8，或者win8以上，或者win7安装了powershell 4.0，5.0，那么 powershell中自带了这样的两个命令，【Invoke-WebRequest】和【Invoke-RestMethod】。

第一个命令返回的是对象，第二个返回的是（整个网页）字符串。

这两个命令有时候会返回乱码，很长一段时间，我认为，是这个命令有解码bug，但后来发现，把结果用其自带的-outfile参数输出到文件之后，编码是正确的。 也就是说，其实是我们不知道怎么解码。只能用写入磁盘的慢方法。

powershell 传教士 原创文章。始于 2016-04-09 允许转载，但必须保留名字和出处，否则追究法律责任

用这个方法写爬虫脚本是最简单的。写入磁盘虽然慢了一点点，但基本不影响我们用之爬数据。

# --------------------正文--------------------

用【Invoke-RestMethod】写爬虫脚本太简单了。步骤为：

1 下载文件

2 打开文件，放入大字符串。

3 根据模板匹配字符串，扣出需要的内容。内容可以是行列标准的表格，也可以不是。

关键就是做好模板，和使用模板命令ConvertFrom-String。需要注意的是，这个是ps5.0新增命令，对于win7来说，要安装ps5.0哦。

ConvertFrom-String  命令的详细用法，请看本人拙作帖子：

ConvertFrom-String 命令研究

[http://www.cnblogs.com/piapia/p/5089332.html](http://www.cnblogs.com/piapia/p/5089332.html)

# --------------------爬虫欣赏--------------------

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
<#
http://www.xicidaili.com/nn/1
http://haodailiip.com
从web页扣取代理服务器的ip端口。（只要ip，端口）
invoke-Request 写入 temp代理001.html，temp代理001.html 读入字符串，convertfrom-string 扣取数据，写入代理001.txt
#>

$script:脚本存储路径 = Split-Path -Parent   $myinvocation.mycommand.path
$Env:Path+=";$script:脚本存储路径;"

$临时文件名 = "temp代理001.html"
$临时文件路径全名 = "$script:脚本存储路径\$临时文件名"

$输出文件名 = "代理001.txt"
$输出文件路径全名 = "$script:脚本存储路径\$输出文件名"

$网址 = 'http://www.xicidaili.com/nn/1'

$模板 =
@'
    <tr class="odd">
      <td></td>
      <td><img src="http://fs.xicidaili.com/images/flag/cn.png" alt="Cn" /></td>
      <td>{IP地址*:171.34.189.91}</td>
      <td>{端口:8118}</td>
      <td>
        <a href="/2016-04-07/jiangxi">江西</a>
      </td>
      <td>高匿</td>
      <td>HTTP</td>
      <td>
        <div title="1.377秒" class="bar">
          <div class="bar_inner fast" style="width:88%">

          </div>
        </div>
      </td>
      <td>
        <div title="0.275秒" class="bar">
          <div class="bar_inner fast" style="width:95%">

          </div>
        </div>
      </td>
      <td>16-04-07 01:45</td>
    </tr>

    <tr class="">
      <td></td>
      <td><img src="http://fs.xicidaili.com/images/flag/cn.png" alt="Cn" /></td>
      <td>{IP地址*:119.188.94.145}</td>
      <td>80</td>
      <td>
        <a href="/2014-11-02/shandong">山东济南</a>
      </td>
      <td>高匿</td>
      <td>HTTPS</td>
      <td>
        <div title="6.157秒" class="bar">
          <div class="bar_inner medium" style="width:38%">

          </div>
        </div>
      </td>
      <td>
        <div title="0.36秒" class="bar">
          <div class="bar_inner fast" style="width:93%">

          </div>
        </div>
      </td>
      <td>16-04-07 01:27</td>
    </tr>
'@


Invoke-RestMethod  -uri $网址 -OutFile $临时文件路径全名      #PowerShell 爬虫步骤1：下载文件
$临时文件 = Get-Content  -raw  -LiteralPath  $临时文件路径全名      #PowerShell 爬虫步骤2：打开文件放入大字符串
#powershell 传教士 2016-04-09 win10测试通过
$结果 = ConvertFrom-String -TemplateContent $模板   -InputObject  $临时文件      #PowerShell 爬虫步骤3：根据模板匹配扣出需要的行列标准内容。关键就是做好模板。
$结果 | Format-Table -AutoSize  | Tee-Object -Append  -FilePath  $输出文件路径全名
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# --------------------终--------------------

安装 powershell 5.1 for win7-sp1-64

1确保你已经安装了.net 4.5以上。如果没装，直接装.net 4.62即可：

Microsoft .NET Framework 4.62(win10红石自带此版本。其他win版本建议立即安装)

https://www.microsoft.com/zh-cn/download/details.aspx?id=53344

Microsoft .NET Framework 4.62 简体中文语言包

https://www.microsoft.com/zh-cn/download/details.aspx?id=53323

2装ps 5.1：

https://msdn.microsoft.com/en-us/powershell/wmf/5.1/install-configure

汝之老家，他就在这个屯，汝是win屯土生土长的人\~\~\~

虽然家里不咋大，却有bat，有vbs，有powershellllllll

家里养的俩爬虫，誓要把那蟒蛇（python）挤回linux去\~\~\~

引用 转帖 的 powershell 爬虫 相关文章：

http://beanxyz.blog.51cto.com/5570417/1784596

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [爬虫](https://www.cnblogs.com/piapia/tag/%E7%88%AC%E8%99%AB/) , [Invoke-WebRequest](https://www.cnblogs.com/piapia/tag/Invoke-WebRequest/) , [Invoke-RestMethod](https://www.cnblogs.com/piapia/tag/Invoke-RestMethod/) , [spider](https://www.cnblogs.com/piapia/tag/spider/)
