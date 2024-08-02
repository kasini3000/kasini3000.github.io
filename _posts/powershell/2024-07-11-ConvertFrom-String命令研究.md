---
categories: powershell
layout: post
permalink: /powershell/ConvertFrom-String命令研究
---

powershell 5.0 中的 ConvertFrom-String 命令 研究,详解,真经,手册,用途 说明 。 字符串变成对象属性后，粒度变小了，并且很容易输出到数据库，excel中。 那是一条神奇的天路啊啊~，令字符的输出变成对象~~， 从此那山不再高，扣输出不再难，字符和对象欢聚一堂。

# -------先上个例子-------

\$aaa =
@'
0.027 0.034 0.834 0.105
0.346 0.558 0.018 0.078
0.001 0.997 0.001 0.001
0.994 0.001 0.004 0.001
0.001 0.996 0.002 0.001
0.001 0.001 0.997 0.001
0.001 0.009 0.001 0.989
0.051 0.111 0.837 0.001
'@

\$t =
@'
{字段名1\*:0.027} {字段名2:0.034} {字段名3:0.834} {字段名4:0.105}
{字段名1\*:0.346} 0.558 0.018 0.078
'@

#【字段名】也可以叫【属性名】
\$bbb = \$aaa | ConvertFrom-String -TemplateContent \$t
\$bbb |Format-Table -AutoSize

-------命令解释-------

ConvertFrom-String
命令的功能和目的：
把【行列整齐字符串】转换成【PSCustomObject】

powershell 传教士 原创文章 2016-01-06 允许转载，但必须保留名字和出处，否则追究法律责任 ConvertFrom-String

脚本必然要进行文本处理，文本处理又没有什么通用的规律可循。祖师爷发明的【模板】法真是太爽了！
字符串变成对象属性后，粒度变小了，并且很容易输出到数据库，excel中，为行列转换提供数据了。
那是一条神奇的天路啊啊\~\~，令字符的输出变成对象\~\~，
从此那山不再高，扣输出不再难，字符和对象欢聚一堂。

# -------模板的具体制作方法-------

模板中，把要分列的内容，用大括号【{}】扩起来，里面加一个冒号，冒号前面是你定义的【属性名】，冒号后面是【属性值】
每行第一个【属性名】后面要加上【\*】，【模板行】至少要有两行例子，至少要有两个【\*】，
例子的距离和真实数据的距离，不用一样。如：

真实数据：
0.027 0.034<-- 这里是距离，有n个空格 -->0.834 0.105

模板：
{字段名1\*:0.027} {字段名2:0.034}<-- 这里是距离，用一个空格，或一个tab即可 --> {字段名3:0.834} 0.105

-------其他例子-------

\$返回数据 = @(netstat -an )
\$返回数据 -replace '^\\s+' | ConvertFrom-String -PropertyNames 协议, 本地IP端口, 外部IP端口, 状态

-------感谢 群友 小楼 分享的例子-------

#Requires -Version 5.0
\$s=@"
张三
25
李四
34
王五
12
赵六
65
"@

\$t=@"
{Name\*:张三}
{Age:25}
{Name\*:李四}
{Age:34}
"@

\$s | ConvertFrom-String -TemplateContent \$t

结果：

Name Age

---

张三 25
李四 34
王五 12
赵六 65

【长老】[小楼](55589641) 12:48:37
ConvertFrom-String的作用是提取文本中的有用信息，转为psobject

\$t=@"
SSID 1 : {bssid\*:abcabc}
Network type : 结构
身份验证 : WPA2 - 个人
加密 : CCMP
BSSID 1 : {macaddress:aa:bb:08:11:33:3c}
信号 : 83%
无线电类型 : 802.11n
频道 : 3
基本速率(Mbps) : 1 2 5.5 11
其他速率(Mbps) : 6 9 12 18 24 36 48 54
SSID 2 : {bssid\*:defdef}
Network type : 结构
身份验证 : WPA2 - 个人
加密 : CCMP
BSSID 1 : {macaddress:cc:dd:e4:7a:c4:d8}
信号 : 64%
无线电类型 : 802.11n
频道 : 6
基本速率(Mbps) : 1 2 5.5 11
其他速率(Mbps) : 6 9 12 18 24 36 48 54
"@

netsh wlan show network mode=bssid | ConvertFrom-String -TemplateContent \$t
【长老】[小楼](55589641) 12:55:47
结果：
bssid macaddress

---

abc-internet b4:a4:e3:65:ea:60
sannongziben1 00:22:aa:ad:0b:44
abcd 64:f6:9d:d5:e4:40
abc-BYOD 64:f6:9d:d5:e4:42
abc-GUEST 64:f6:9d:d5:e4:41
sharmoon 78:a1:06:58:6d:82
sannongziben666 00:22:aa:ad:1c:d8
TP-LINK-88SL ec:26:ca:30:78:dc
abcde 00:1f:33🇩🇪22:ae
TP-LINKlongten 14:75:90:bd:9a:cc
weijin 14:75:90:43:96:fe
CTC-INTER 14:cf:92:62:76:d4
D-Link\_DIR-600M 34:08:04:7d:2a:b1

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [ConvertFrom-String](https://www.cnblogs.com/piapia/tag/ConvertFrom-String/)
