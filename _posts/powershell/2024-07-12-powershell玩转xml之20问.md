---
categories: powershell
layout: post
permalink: /powershell/powershell玩转xml之20问
---

powershell 传教士 原创文章  2014-01-30,2015-12-28，2019-02-28改 允许转载，但必须保留名字和出处，否则追究法律责任

问：xml文件编码情况如何？
答：为了更好的支持多字符，xml文件都是utf8或unicode格式了，用bat处理不了了。

问：xml文件最基本的格式如何？
答：

<?xml version="1.0" encoding="UTF-16"?>

<根节点>
<根节点/>
如上所述，必须有，且只有一个根节点。并把这个文件保存成unicode格式。
当然utf-8也行。

问：xml文件都含有哪些子项目？
答：
元素（目录）======element
属性=========attrib
文本=========text
节点=========XmlNode
注释
CDATA========不需要转义的内容
【Processing Instruction】======存命令名和命令值

问：powershell 和 。net如何处理xml，有何优势？
答：传统方法处理xml，是拿xml当纯文本文档来处理，即用正则来查找替换。
而xml文档是有结构的，xml文档这个目录结构，。net中叫做xpath。powershell和。net用xpath灵活快速在xml文件中定位，然后读写内容。
这就是他们之间的最根本区别。
.net处理xml有好几个类：
system.xml.XmlDocument，
https://docs.microsoft.com/zh-cn/dotnet/api/system.xml.xmldocument?view=netframework-4.7.2

System.Web.UI.WebControls.Xml，
https://docs.microsoft.com/zh-cn/dotnet/api/system.web.ui.webcontrols.xml?view=netframework-4.7.2

asp.net 里好像也有xml类，具体忘了。
有兴趣的可以研究其中的异同点。

问：.net读写xml有几种方法？
答：两种 1 xmlreader，xmlwriter。2用dom法。这里主要讨论dom法。

问：如何打开xml文件？
答：
\$输入文件名 = "a:\\pscode\\test74.xml"
[system.xml.XmlDocument]\$打开的xml文件 = Get-Content \$输入文件名

问：如何保存xml文件？
答：
\$输出文件名 = "a:\\pscode\\test74.xml"
\$打开的xml文件.save(\$输出文件名)

问：打开保存后，xml文件编码如何？
答：跟源文件相同。即源文件是utf16，存后也是utf16。原来是utf8，存后也是utf8

问：如何选择节点？
答：

第1种方法
if  (\$打开的xml文件.SelectSingleNode("/节点名1/节点名2") -ne \$null)
{
\$已选择的节点 = \$打开的xml文件.SelectSingleNode("/节点名1/节点名2")
}
或
\$xml根节点 = \$打开的xml文件.SelectSingleNode("/")
\$xml根节点.节点1.节点2[0].name

问：什么是xpath？xpath语法是？
答：
xml文档操作，很大程度上在于选择节点，这个节点的选择，使用xpath语法。注意 xpath 区别大小写
更多的xpath节点选择语法参考，在msdn官网。
https://docs.microsoft.com/zh-cn/previous-versions/dotnet/netframework-4.0/ms256471(v%3dvs.100)

"节点名1/节点名2[1]"--------返回节点2中，第一个元素值
"节点名1/节点名2[元素<3]"--------返回节点2中，元素小于3的所有值
"节点名1/节点名2[last()]"--------返回节点2中，最后一个元素值

"节点名1/节点名2[\*]"--------返回节点中，所有元素值
"节点名1//节点名2"--------返回节点1中，任意层次，含有节点2的内容
"节点名1 | 节点名2"--------返回节点1，节点2的集合
"节点名1[@abc] "--------返回节点1中，所有带有@abc属性的集合

问：如何选择节点？
答：

第2种方法
powershell有个xml处理命令Select-Xml，用来选择节点，相对比较简单。
\$值1 = (select-xml -Path \$输入文件名 -XPath "/aaa/bbb[1]/Ccc").node.最后一个节点
\$值2 = (select-xml -Path \$输入文件名 -XPath "/aaa/bbbB").node.最后一个节点
或
[xml]\$xml根节点 = (select-xml -Path \$输入文件名 -XPath "/").node
\$xml根节点.aaa.bbbB

问：xml常用对象，方法，手册在哪？
答：
https://docs.microsoft.com/zh-cn/dotnet/api/system.xml.xmlelement?view=netframework-4.7.2

元素===目录===element的常用方法和属性：

属性
HasChildNodes 获取一个值，该值指示此节点是否有任何子节点。
ChildNodes 获取节点的所有子节点。
InnerText 获取或设置节点及其所有子级的串联值
InnerXml 获取或设置只表示此节点子级的标记。
IsEmpty 获取或设置元素的标记格式。
OuterXml 获取包含此节点及其所有子节点的标记。

方法
GetAttribute(String) 返回具有指定名称的特性的值。
GetEnumerator 获取循环访问当前节点中子节点的枚举
HasAttribute(String) 确定当前节点是否具有带有指定名称的特性。
RemoveAttribute(String) 按名称移除特性。
RemoveChild 移除指定的子节点。
SetAttribute(String, String) 设置具有指定名称的特性的值。

问：请给出xml文件例子。
答：
----

<?xml version="1.0" encoding="utf-8"?>

<根>
　　<三大晒娃狂魔>
　　　　<李小璐>
　　　　　　<!--Comment是注释--><![CDATA[在CDATA节中，所有的字符，包括特殊字符都不需要转义，这样察看和修改XML文档都比较方便。]]><?这李是命令名 命令值?>

　　　　</李小璐>
　　　　<徐若瑄>

　　　　　　text值=儿子李v宝

　　　　</徐若瑄>
　　　　<范玮琪 属性值-出生地="美国俄亥俄州">

　　　　</范玮琪>

　　　　三大晒娃狂魔中的text值=晒娃是指把孩子的照片等贴上微博，刷微信等进行炫耀。
　　</三大晒娃狂魔>
</根>
-----

问：如何增，删，改注释？
答：
----

\$输入文件名 = 'a:\\pscode\\l例子.xml'
\$输出文件名 = 'a:\\pscode\\l例子2.xml'
[system.xml.XmlDocument]\$打开的xml文件 = Get-Content \$输入文件名

if (\$打开的xml文件.SelectSingleNode("/根/三大晒娃狂魔") -ne \$null)
{
\$已选择的节点 = \$打开的xml文件.SelectSingleNode("/根/三大晒娃狂魔")
}

[string]\$要添加的注释内容 = '到底谁是第四个晒娃狂魔捏？'
\$null = \$要添加的注释对象 = \$打开的xml文件.CreateComment(\$要添加的注释内容)
\$null = \$已选择的节点.AppendChild(\$要添加的注释对象)
#移除指定的子节点，用RemoveChild(对象)
#编辑对象用，ReplaceChild(新,旧)

\$打开的xml文件.save(\$输出文件名)
----------------------------------

问：如何增，删，改【CDATA】？
答：
原理同上
\$null = \$CDATA = \$打开的xml文件.CreateCDataSection(cdata名称)

问：如何增，删，改【Processing Instruction】？
答：
\$null = \$pi = \$打开的xml文件.CreateAttribute("pi名称","pi值")

问：如何增，删，改元素？（节点即目录）
答：
\$null = \$要添加的节点对象 = \$打开的xml文件.CreateElement("节点名称")

问：如何增，删，改属性和值？
答：
\$null = \$要添加的属性对象 = \$打开的xml文件.CreateAttribute("属性名称")

问：如何写入元素值？
答：
if  (\$打开的xml文件.SelectSingleNode("节点名") -ne \$null)
{
\$打开的xml文件.SelectSingleNode("节点名").元素名 = \$元素值
}

问：如何读节点内的纯文本
答：
\$节点1.InnerText

问：如何读节点内的xml文本
答：
\$节点1.InnerXml

powershell 传教士 原创文章 允许转载，但必须保留名字和出处，否则追究法律责任

问：如何整理xml文件的缩进？
问：我有一个大的XML文件，所有东东都在一行里面，有几百兆了。怎么给加上合理的换行捏？---几百兆？我见过最大不到10M！
答：用powershell第三方模块pscx，http://pscx.codeplex.com/ ，中的format-xml命令。
答：打开，编辑，再保存，就自动整理了。

问：我想把对象导出到xml如何做？
答：
Get-Process | Export-Clixml -Path a:\\abc.xml

问：反过来从xml导入呢？
答：
\$aaa = Import-Clixml -Path a:\\abc.xml

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [xml](https://www.cnblogs.com/piapia/tag/xml/) , [.net](https://www.cnblogs.com/piapia/tag/.net/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)

