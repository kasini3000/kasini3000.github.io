---
categories: powershell
layout: post
permalink: /powershell/powershell玩转sqlite数据库
---

脚本经常需要处理文本，有时候是行列整齐文本。那么powershell脚本处理行列文本有几种方法呢？一种是excel，另外的一些是？access？sqlite？

sqlite是一个很小巧的，很方便嵌入到脚本语言中的，关系型数据库。它给powershell提供了一个免费的，处理行列整齐数据，存储数据，统计数据的极品神器。
只要你学会了它，上天入地皆可去得。而且以后学sql server，mysql，都类似，没有难度。

带着引号搜本文关键字： "^\_^上天入地皆可去^\_^"

发文初衷：世界上几乎没有讲powershell调用sqlite的帖子。

初稿，会更新。有错误欢迎指正，谢谢。

powershell,sqlite,数据库,database,powershell传教士

# ------------------【sqlite 字段类型】------------------

sqlite只有5种数据类型，用于存储。
数据类型 占用字节 描述
NULL
TEXT ？ 使用数据库编码（UTF-8，UTF-16BE或者UTF-16LE）存放
INTEGER 根据值的大小以1,2,3,4,6或8字节存放 值是有符号整形
REAL 以8字节IEEE浮点数存放
BLOB 最多 1,000,000,000 字节 只是一个数据块，完全按照输入存放（即没有准换）存放。

使用时会将【内存数据类型】和【存储数据类型】之间进行转换。
如将布尔值，转换成INTEEGER存储。具体参见手册：
http://www.sqlite.org/datatype3.html
没有日期型，没有布尔型，这一点要注意。

# ------------------【应用场景，重要！】------------------

powershell 传教士 原创文章 始作于 2016-01-01 元旦， 改 允许转载，但必须保留名字和出处，否则追究法律责任

下为个人整理的应用场合，如有不妥，请斧正。

1 存储数据。中量数据。
这个中量数据是指，由于sqlite表不支持分区，所以应该在一亿条以下，或者更少。
excel表存储数据有个行数（记录条数）限制，104万左右。比如我有110万数据，只有一列，要统计列最大值。这时用excel就不方便了。
excel中每单元格最大存储32k字节，access2016中，每个text字段的容量存储为64k字节，行数倒是够用。

2 各种数据统计。
3 由于sqlite支持纯内存数据库，把所有库放在内存中。可以结合上述两种需求。即从大库查询（或导出）部分数据，在内存中进行统计。
4 缺点是：数据展示不好。不像excel、access那样（对业务人员）有表，图。
5 sqlite是关系型数据库，join，group by，order by这些基本功能和mysql，sql server比不逊色。单线程性能也很好。另外单机，绿色，软件1M以下，支持数据量极大，比access强。

------------------【图形管理工具】------------------

英文：
SQLiteStudio http://sqlitestudio.pl/
SQLiteManager http://www.sqlabs.com/
SQLite Expert http://www.sqliteexpert.com/
SQLite Developer http://www.sqlitedeveloper.com/
SQLiteSpy http://www.yunqa.de/delphi/doku.php/products/sqlitespy/index
RazorSQL http://www.razorsql.com/

中文：
SQLite Administrator http://sqliteadmin.orbmu2k.de/ 打不开3.x版本数据库
navicat for sqlite http://www.xiazaiba.com/html/3350.html 推荐
Database.NET http://fishcodelib.com/database.htm 推荐

# ------------------【下载安装】------------------

ps调用sqlite的两种方法。这里我们主要介绍第二种
1 https://psqlite.codeplex.com/
2 https://github.com/RamblingCookieMonster/PSSQLite
上述2种，都支持支持winxp，powershell2.0，及以上。

2.1 下载：
https://github.com/RamblingCookieMonster/PSSQLite/archive/master.zip

2.2 建立【C:\\Users\\你的用户名\\Documents\\WindowsPowerShell\\Modules】目录，并解压：
把 PSSQLite-master\\PSSQLite 这个目录复制到，你的模块目录中。
即modules目录中，含有PSSQLite这个目录。

2.3 查看模块中的所有命令：
Import-Module pssqlite
get-command \* -module pssqlite

返回
CommandType Name Version Source

---

Function Invoke-SQLiteBulkCopy 1.0.1 pssqlite
Function Invoke-SqliteQuery 1.0.1 pssqlite
Function New-SQLiteConnection 1.0.1 pssqlite
Function Out-DataTable 1.0.1 pssqlite

2.4 一键安装命令：
#一键安装命令依赖.net 4.5 及以上。powershell 3.0 及以上。如果不满足条件，请用手动安装方法。
mkdir "\$env:USERPROFILE\\Documents\\WindowsPowerShell\\Modules"
Invoke-WebRequest https://github.com/RamblingCookieMonster/PSSQLite/archive/master.zip -OutFile .\\master.zip
add-Type -AssemblyName 'System.IO.Compression.Filesystem'
[System.IO.Compression.ZipFile]::ExtractToDirectory("\$pwd\\master.zip","\$pwd")
Copy-Item -Recurse PSSQLite-master\\PSSQLite "\$env:USERPROFILE\\Documents\\WindowsPowerShell\\Modules"
Import-Module pssqlite
get-command \* -module pssqlite

# ------------------【命令帮助】------------------

Invoke-SqliteQuery -DataSource \$库文件名 -query \$查询字符串 -as 输出格式(PSObject,DataRow,DataTable,DataSet,SingleValue)

\$内存库 = New-SQLiteConnection -DataSource :MEMORY:
Invoke-SqliteQuery -SQLiteConnection \$内存库 -Query "CREATE TABLE OrdersToNames (OrderID INT PRIMARY KEY, fullname TEXT);" #建表语句
Invoke-SqliteQuery -SQLiteConnection \$内存库 -Query "INSERT INTO OrdersToNames (OrderID, fullname) VALUES (1,'Cookie Monster');" #插入语句
Invoke-SqliteQuery -SQLiteConnection \$内存库 -Query "SELECT xxx FROM yyy WHERE mmm=nnn;" #你想要的统计语句

问：csv如何入库？
答：
最好用图形工具搞，也可以用下列类似命令。
\$dt1 = get-process | Out-DataTable
Invoke-SQLiteBulkCopy -DataTable \$st1 -DataSource \$DataSource -Table 表名
具体参见例子2

------------------【powershell操作sqlite经验】------------------

1 不外乎查询出行，查询出列， 或查询出表，然后foreach。
2 不外乎去重，合并结果，统计最大，列中数字合计。

问：乍开始我不熟悉sql语句咋办？
答：
用拐棍！完全使用图形程序（找传教士要navicat for sqlite）来建表，导入数据，在图形软件中运行sql。

问：现在我已经会用【常用sql语句】+【图形程序】来处理数据了。为啥还要用脚本呢？
答：
图形程序毕竟只是根拐杖。你要处理的数据有可能要先取到一行，（比如用"\_"先拆分，分成n列）预先用ps处理后再入库。
库里输出的行列，也很容易（用Out-GridView进行展示等。）所以说还是要ps+sqlite共同处理的。

---------------【根据官网手册，增减的sql教程】----------------

sqlite官网，手册位置：
http://www.sqlite.org/lang.html

问：什么是（关系型）数据库索引，索引有什么好处坏处？
答：
索引是把无序的每条数据，添加了一个从小到大的顺序，变成了有序的数据。
每个字段都可以添加一个索引。或者n个字段公用一个索引。
索引加速了查询速度，降低了插入速度。

问：如何快速插入数据？
答：
1 较少并发写入，最好只有一个线程在读写。
2 尽量减少索引。
3 使用事务。事务是由类似【START TRANSACTION】，【COMMIT】sql关键字包含的命令。
事务的作用是，在所有数据插入后，即【COMMIT】后，才一次性修改所有索引，而不是插入一条，修改一次索引。
4 使用.net连接器封装的事务。即类似sql bulkcopy 之类的方法。

问：事务相关的sql语句是？
答：
BEGIN TRANSACTION; 事务开始
COMMIT TRANSACTION; 显示事务被提交，数据表中的数据也发生了变化。
ROLLBACK TRANSACTION; 回滚

问：为什么传教士也很爱sqlite？
答：
PRAGMA encoding; # 这个命令将返回库编码默认值，utf-8
用 中文表名，字段名毫无问题！！！

问：打开库文件名后，列出所有《库名》的sql语句是？
答：
PRAGMA database\_list;

问：打开sqlite库文件后，默认的数据库名是？
答：
main

问：列出所有表名的sql语句是？
答：
SELECT name FROM sqlite\_master WHERE type='table' order by name;

问：测试表是否存在的sql语句是？
答：
SELECT name FROM sqlite\_master WHERE name='表名' ;

问：列出某个表的所有索引，要用的sql语句是？
答：
PRAGMA index\_list(表名);

问：建立表的sql语句是？
答：
CREATE TABLE IF NOT EXISTS '表名' ('自增' INTEGER PRIMARY KEY AUTOINCREMENT, '字段名2' REAL)
CREATE TABLE IF NOT EXISTS '表名' ('字段名1' INTEGER AUTOINCREMENT, '字段名2' REAL, '字段名3' TEXT, '字段名4' BLOB,PRIMARY KEY ('字段名1'))
CREATE TEMP TABLE #建临时表
PRIMARY KEY #主键
AUTOINCREMENT #自增

问：建立，删除索引的sql语句是？
答：
CREATE INDEX IF NOT EXISTS 索引名 ON 表名(字段名)
CREATE INDEX IF NOT EXISTS 索引名 ON 表名(字段名 ASC,字段名2 DESC)
CREATE UNIQUE INDEX IF NOT EXISTS 索引名 ON 表名(字段名 DESC)#唯一索引

删除索引：
DROP INDEX IF EXISTS 索引名

传教士问：
我搜遍了全世界，包括官方，也搞不清楚为啥不是DROP INDEX IF EXISTS 索引名 on 表名？而是DROP INDEX IF EXISTS 数据库名.索引名
一个库里，可以有多个表啊，请问谁能答？

问：什么是sqlite数据清理？有什么好处？
答：
删除一条记录时只是打上删除标记。数据清理是彻底删除数据。
大量的插入、更新和删除等操作后，清理下数据，有助于读取性能。还会归还空间给操作系统。
sqlite的数据清理，只能对整个数据库来操作。

问：数据清理命令是？
答：
Attached 数据库名 连接数据库。
vacuum 删除已经打上删除标记的表，记录。
reindex 重生成索引

问：查看表结构的sql语句是？
答：
PRAGMA table\_info(表名);

问：清空表的sql语句是？
答：
delete from 表名;
update sqlite\_sequence SET seq = 0 where name ='表名'; #自增字段归零。sqlite\_sequence是main库中的特殊表。

问：插入数据的sql语句是？
答：
INSERT INTO 表名 VALUES(null,21.5,'this is a texttest.','blob WER@#\$@\$%^^')

问：空值是？
答：
null

http://www.sqlite.org/lang\_select.html
问：查询数据的sql语句是？
答：
SELECT '字段名' FROM '表名' WHERE 条件

问：查询结果去重的sql语句是？
答：
SELECT DISTINCT '字段名'
SELECT DISTINCT '字段名1','字段名2','字段名3','字段名4' #所有字段的值完全相同，才算重复。

问：集合去重的sql语句是？
答：
SELECT 返回的集合1
UNION
SELECT 返回的集合2

问：查询结果按 '字段名' 分组输出的sql语句是？
答：
SELECT '字段名' FROM '表名' WHERE 条件 GROUP BY '字段名' ;
GROUP BY 一定要放在WHERE之后，GROUP BY 一定要放在ORDER BY之前。

问：查询结果按 '字段名' 排序输出的sql语句是？
答：
SELECT '字段名' FROM '表名' WHERE 条件 ORDER BY '字段名' ASC ;
SELECT '字段名' FROM '表名' WHERE 条件 ORDER BY '字段名' DESC ;

问：隐藏的自增列叫什么？
答：
rowid或\_rowid\_
在没有INTEGER类型的主键时，存在这个隐藏字段。

问：sqlite有个叫sqlite3.exe的客户端软件。我可以用这个sqlite.exe + cmd + bat 玩转sqlite数据库么？
答：
基本不行。
简单来说powershell有内置的值类型，如小数数值，和表datetable。而bat中没有。
powershell字符串对象长度大，bat数据超过8000左右字母不行。

# ------------------【例子】------------------

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
# 例子1 ：专门给菜鸟编写的 PowerShell 操作Sqlite的例子。大量硬编码，快速上手。win10测试通过。
Import-Module pssqlite


$sqlite库文件名 = "a:\pscode\sqlite\test001.db" # 内存库用 ':MEMORY:'
$sqlite库名 = "" #默认main
$sqlite表名 = "biao001"

#region sql
#---
$建立_表_的sql语句 = ""
$建立_索引_的sql语句 = "CREATE INDEX IF NOT EXISTS $将要建立的索引名 ON $sqlite表名($将要建立索引的字段名)"
#powershell 传教士 原创例子 始作于 2016-01-01 元旦 允许转载，但必须保留名字和出处，否则追究法律责任
#---
$列出所有_库名_的sql语句 = 'PRAGMA database_list;'
$列出所有_表名_的sql语句 = "SELECT name FROM sqlite_master WHERE type='table' order by name;"
$列出某个_表结构_的sql语句 = "PRAGMA table_info($sqlite表名);"
$列出某个_表的所有索引_的sql语句 = "PRAGMA index_list($sqlite表名); "

#---
$测试_表是否存在_的sql语句 = "SELECT name FROM sqlite_master WHERE name='$sqlite表名' ; "

#---
$插入一条_数据数据_的sql语句 = ''

#---
$查询_表_的sql语句 = ''

#---
$清空_表_的sql语句 = "delete from $sqlite表名 ;"
#---
#endregion sql


# 前提1 库文件确实存在，有【$sqlite库文件名】。则建立连接。
# 前提2 库文件不存在，有【$sqlite库文件名】和【$建立_表的sql语句】。则建立连接，然后建立表。
# 前提3 有【$建立_表的sql语句】和【$sqlite库文件名 = ':MEMORY:'】字符串。则建立连接，然后建立表。
if ($sqlite库文件名 -eq ':MEMORY:')
{
    Write-Verbose  '新建【内存】sqlite库文件'
    $我的Sqlite连接 = New-SQLiteConnection -DataSource $sqlite库文件名
    Invoke-SqliteQuery -query $建立_表的sql语句  -DataSource $sqlite库文件名
}
else
{
    if (test-path $sqlite库文件名)
    {
        Write-Verbose  'sqlite库文件已经存在'
        $我的Sqlite连接 = New-SQLiteConnection -DataSource $sqlite库文件名
    }
    else
    {
        Write-Verbose  '新建sqlite库文件'
        $我的Sqlite连接 = New-SQLiteConnection -DataSource $sqlite库文件名

        #新建表
        $字段字串 = ""
        $建立_表_的sql语句 =
@"
CREATE TABLE IF NOT EXISTS '$sqlite表名' ($字段字串)
"@
        Invoke-SqliteQuery -query $建立_表_的sql语句  -DataSource $sqlite库文件名
    }
}

# 列出库，列出表。前提【$我的Sqlite连接】已经建立。
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $列出所有_库名_的sql语句
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $列出所有_表名_的sql语句



# 建表索引。前提【$sqlite表名】，【$将要建立的索引名】，【$将要建立索引的字段名】已经赋值。
$将要建立的索引名 = 'yyy'
$将要建立索引的字段名 = 'bbb'
$建立_索引_的sql语句 = "CREATE INDEX IF NOT EXISTS $将要建立的索引名 ON $sqlite表名($将要建立索引的字段名)"
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $建立_索引_的sql语句



# 测表是否存在，查表结构，查表索引。前提【$sqlite表名】已经赋值。
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $测试_表是否存在_的sql语句 #返回表名
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $列出某个_表结构_的sql语句
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $列出某个_表的所有索引_的sql语句



$查询_表_的sql语句 = "select * from  '$sqlite表名'     ;"
$查询_表_的sql语句
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $查询_表_的sql语句



#插入数据
$字段1值 = 'null'
$字段2值 = '121215687'
$字段3值 = ''
$插入一条_数据数据_的sql语句 = "INSERT INTO $sqlite表名 VALUES($字段1值,$字段2值) ;" #本例表只有2个字段
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $插入一条_数据数据_的sql语句



#查询数据
$查询_表_的sql语句 = "select * from  '$sqlite表名'  ;"
$查询_表_的sql语句
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $查询_表_的sql语句



#清空
$清空_表_的sql语句 = "delete from $sqlite表名 ;"
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $清空_表_的sql语句
Invoke-SqliteQuery -SQLiteConnection $我的Sqlite连接 -Query $查询_表_的sql语句

```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
# 例子2：专门给菜鸟编写的 PowerShell 操作Sqlite bulkcopy的例子。大量硬编码，快速上手。win10测试通过。
Import-Module pssqlite

$sqlite库文件名 = "a:\pscode\sqlite\test006.db" # 内存库用 ':MEMORY:'
$sqlite库名 = "" #默认main
$sqlite表名 = "biao001"

if (test-path $sqlite库文件名)
{
    Write-error  'sqlite库文件已经存在'
    exit 1
}

$点net的DataTable = get-process | Out-DataTable   #数据来源

$列集合 = $点net的DataTable.Columns
[array]$每个字段名 = @()
foreach ($temp001 in $列集合)
{
    $每个字段名 += $temp001.ColumnName
}

#powershell 传教士 原创例子 始作于 2016-01-01 元旦 允许转载，但必须保留名字和出处，否则追究法律责任

$每个字段类型 = 'text'
$temp002 = $每个字段名[0]
$建立_表_的sql语句 =
@"
CREATE TABLE IF NOT EXISTS '$sqlite表名' ($temp002  $每个字段类型)
"@
#Write-Warning $建立_表_的sql语句
Invoke-SqliteQuery -query $建立_表_的sql语句  -DataSource $sqlite库文件名

for ($i = 1;$i -le $每个字段名.Count ;$i++)
{
    $temp003 = $每个字段名[$i]
    $建立_表_的sql语句 =
@"
ALTER TABLE  '$sqlite表名' ADD COLUMN '$temp003'  $每个字段类型 ;
"@
#    Write-Warning $建立_表_的sql语句
    Invoke-SqliteQuery -query $建立_表_的sql语句  -DataSource $sqlite库文件名
}

Invoke-SQLiteBulkCopy -DataTable $点net的DataTable -DataSource $sqlite库文件名 -Table $sqlite表名 -force
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

------------【powershell sqlite 模块 扩展阅读】---------------

问：sqlite模块中的dll库文件有哪些？哪来的？
答：
模块中有两个库文件，分别是【SQLite.Interop.dll】和【System.Data.SQLite.dll】。
下载地址在：
http://system.data.sqlite.org/index.html/doc/trunk/www/downloads.wiki

问：【SQLite.Interop.dll】是个啥玩意？
答：
我说过，只要这个数据库兼容.net，就可以很容易地被powershell使用。
【SQLite.Interop.dll】是个.net用户调用sqlite的（中介）插件。
通过它调用【System.Data.SQLite.dll】中的System.Data.SQLite类。

问：如何升级这两个库呢？
答：
一般来讲无需升级。若要升级就要分32，64位。然后要看.net版本。还得选静态库，或动态库。
选的话当然32位，64位，都要选。选静态库。
win7中.net 4.51已经淘汰了，所以应该选4.5.2编译的。但是呢，没有4.5.2的。那我们就只能选4.5.1的了。
而win10中，应选4.6的。

----------------【完】----------------

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [数据库](https://www.cnblogs.com/piapia/tag/%E6%95%B0%E6%8D%AE%E5%BA%93/) , [powershell传教士](https://www.cnblogs.com/piapia/tag/powershell%E4%BC%A0%E6%95%99%E5%A3%AB/) , [sqlite](https://www.cnblogs.com/piapia/tag/sqlite/) , [database](https://www.cnblogs.com/piapia/tag/database/)
