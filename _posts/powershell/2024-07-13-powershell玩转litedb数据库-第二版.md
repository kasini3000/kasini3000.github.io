---
categories: powershell
layout: post
permalink: /powershell/powershell玩转litedb数据库-第二版
---

powershell可以玩nosql数据库吗？答案是肯定的。只要这个数据库兼容.net，就可以很容易地被powershell使用。

发文初衷：世界上几乎没有讲powershell调用nosql的帖子，更没有讲powershell调用litedb的。只有寥寥powershell调用monodb的代码。

2017-10-08更新，文章和脚本支持litedb 4.0。有错误欢迎指正，谢谢。

powershell,nosql,litedb,bson,数据库,powershell传教士

# ---【存储格式】---

BSON是一种类似json的一种二进制形式的存储格式，简称Binary JSON。 它和JSON一样，支持内嵌的文档对象和数组对象，但是BSON有JSON没有的一些数据类型，如Date和BinData类型。

{"hello":"world"} 这是一个BSON的例子， 其中"hello"是键名，它一般是string类型， 后面的"world"是键值，它的类型一般是string,double,array,binarydata等类型。

bson存储记录的最大单位是Document，可以把这个Document理解为一条记录。

根据litedb手册中的说明，限制为： 每个数据库小于2GB；每个document小于1MB；

即 litedb---》表---》bson\_Document---》bson\_Document---》键值对

由于值可以是空，所以每个bson\_Document最小必须含有一个键。如：{"aaa":""}

# ---【应用场景，重要！】---

下为个人整理的应用场合，如有不妥，请斧正。

1 单机，绿色，中小量数据。单个库总内容小于1.8GB。

2 类似于sqlite，但sqlite是sql数据库，这个是nosql。即键值对，即每个库中数据只有一列。 即nosql数据库不适合存储行列数据。但数组，哈希表，和之间对象转换比sql数据库方便。 查询数据不用sql语法用函数。

3 需要存储变量名，变量值，的应用。如拿litedb数据库当ini文件用，当xml文件用。

# ---【图形管理工具】---

https://github.com/falahati/LiteDBViewer
https://github.com/JosefNemec/LiteDbExplorer     推荐

# ---【下载安装】---

1 官网为：             http://www.litedb.org/

2 github地址为：       https://github.com/mbdavid/LiteDB/

# ---【常用对象列表】---

LiteDB.LiteDatabase

LiteDB.BsonDocument

LiteDB.BsonMapper    bson键值对<--->对象转换者类

LiteDB.JsonSerializer  bson键值对<--->json键值对转换类。

LiteDB.BsonArray    bson数组

LiteDB.BsonValue    bson值 布尔，日期，guid，object对象,bson值，字节数组，int32，int64，double，字符串，list，字典，等。

LiteDB.LiteFileStorage 存储文件

LiteDB.Query      查询类

# ---【常用函数列表】---

摘自 http://www.cnblogs.com/asxinyu/p/dotnet\_Opensource\_project\_LiteDB.html

------Query----- Query.All 返回所有的数据，可以使用指定的索引字段进行排序

Query.EQ 查找返回和指定字段值相等的数据

Query.LT/LTE 查找< 或 <= 某个值的数据

Query.GT/GTE 查找> 或 >= 某个值的数据

Query.Between 查找在指定区间范围内的数据

Query.In - 和SQL的in类似吧，查找和列表中值相等的数据

Query.Not - 和EQ相反，是不等于某个值的数据

Query.StartsWith 查找以某个字符串开头的数据

Query.Contains 查找包括某个字符串的数据，这个查询只扫描索引

Query.And 2个查询的交集

Query.Or 2个查询结果的并集

------linq-----

FindAll: 查找表或者集合中所有的结果记录

FindOne:返回第一个或者默认的结果

FindById: 通过索引返回单个结果

Find: 使用查询表达式或者linq表达式查询返回结果

Count() , Exists(),Min() , Max()

# ---【例子，重要！】---

简单来说就是用字符串，日期等，构造键值对。用n个键值对组成bson\_Document，然后增删查改这个bson\_Document。

下列脚本 win10 + powershell 5.1  + litedb 4.0 测试通过。

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#  powershell_for_litedb  v4.0 2017-10-08
$script:脚本存储路径 = Split-Path -Parent   $myinvocation.mycommand.path
$Env:Path += ";$script:脚本存储路径;"

$script:数据库客户端目录 = $script:脚本存储路径
$script:数据库客户端文件名 = 'LiteDB.dll'
$script:数据库客户端绝对文件名 = $script:数据库客户端目录 + '\' + $script:数据库客户端文件名

$script:数据库文件名 = 'test001.db'
$script:数据库绝对文件名 = $script:数据库客户端目录 + '\' + $script:数据库文件名



if (test-path  $script:数据库客户端绝对文件名)
{
    try
    {
        Add-Type -LiteralPath $script:数据库客户端绝对文件名
        write-host "---LiteDB程序载入完成---"
    }
    catch [Exception]
    {
        $error[0].Exception.LoaderExceptions
        Write-Error "加载 $script:数据库客户端文件名 出错！"
        exit 2
    }
}
else
{
    Write-Error "找不到 $script:数据库客户端文件名 ！"
    exit 1
}

$数据库001 = [LiteDB.LiteDatabase]::new($script:数据库绝对文件名)
[string]$查询表名 = $数据库001.GetCollectionNames()
if ($查询表名 -eq "")
{
    write-host "空数据库，无表"
}
else
{
    $查询表名
}

#打开表，没有的话新建表
$表001 = $数据库001.GetCollection("aaaa")
#$查询结果 = $表001.findall()
#$查询结果

# ---------------插入-----------------
$字符串 = 'aa'
$数字 = 123
$日期 = get-date
$真假 = $true
$数组 = "a","1","kkk"

$哈希表2 = @{
    name  = $字符串;
    age   = $数字 ;
    bron  = $日期 ;
    sex   = $真假 ;
    array = $数组;
    嘻嘻    = '哈哈'
}

$json_哈希表2 = ConvertTo-Json -InputObject  $哈希表2
$bson_哈希表2 = [LiteDB.JsonSerializer]::Deserialize($json_哈希表2)
$bson_哈希表2["_id"] = [LiteDB.ObjectId]::NewObjectId() #必须给bson_document,建一个id。

$表001.Insert($bson_哈希表2)


# -------------插入键值对-------------
[LiteDB.BsonValue]$值2 = 'powershell 传教士 原创文章 2017-10-08，允许转载，但必须保留名字和出处，否则追究法律责任传教士。初稿，有错误欢迎指正，谢谢。'
$键2 = '声明'
$bson_Document = [LiteDB.BsonDocument]::new()
$bson_Document["_id"] = [LiteDB.ObjectId]::NewObjectId()
$null = $bson_Document.add($键2,$值2)
$表001.Insert($bson_Document)


#------------------查询--------------------
#查询必须输入键名，键值作为参数。
#返回的都是bson_Document，转换成json，再转换成哈希表对象。再取值。
$查询结果1 = $表001.find([LiteDB.query]::eq("name","aa"))
"---1---"
$查询结果1


$查询结果2 = $表001.find([LiteDB.query]::gt("age","124"))
"---2---"
$查询结果2


$查询结果3 = $表001.find([LiteDB.query]::StartsWith("嘻嘻","哈"))
"---3---"
$查询结果3
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

DotNet开源大本营  写的两篇 litedb的介绍帖子

[.NET平台开源项目速览(3)小巧轻量级NoSQL文件数据库LiteDB](http://www.cnblogs.com/asxinyu/p/dotnet_Opensource_project_LiteDB.html)

[.NET平台开源项目速览(7)关于NoSQL数据库LiteDB的分页查询解决过程](http://www.cnblogs.com/asxinyu/p/dotnet_Opensource_project_LiteDB_Data_Page.html)

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [nosql](https://www.cnblogs.com/piapia/tag/nosql/) , [litedb](https://www.cnblogs.com/piapia/tag/litedb/) , [bson](https://www.cnblogs.com/piapia/tag/bson/) , [数据库](https://www.cnblogs.com/piapia/tag/%E6%95%B0%E6%8D%AE%E5%BA%93/) , [powershell传教士](https://www.cnblogs.com/piapia/tag/powershell%E4%BC%A0%E6%95%99%E5%A3%AB/)

