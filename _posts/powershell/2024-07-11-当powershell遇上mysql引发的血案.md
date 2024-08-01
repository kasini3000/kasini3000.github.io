---
categories: powershell
layout: post
permalink: /powershell/当powershell遇上mysql引发的血案
---

讲的是mysql的命令行程序依赖【<】，【>】的事。我的观点是一个命令行程序，不应该依赖【<】，【>】，这是错误的设计，且其他程序没有这赖皮习惯。欢迎凡是玩数据库的园友参与讨论。

# ------------------【故事来由】------------------

起因：
群友问：把
cmd.exe /c a:\\mysql5.6\\bin\\mysql.exe -uroot < a:\\xxx\\yyy.sql
替换成
powershell.exe /c a:\\mysql5.6\\bin\\mysql.exe -uroot < a:\\xxx\\yyy.sql
就不行了。

经研究发现是由于powershell不支持【<】号导致的。

解决方法：
代替小于号的方法为，用source命令或用cmd。
start-process -FilePath 'a:\\mysql5.6\\bin\\mysql.exe' -ArgumentList ' -uroot -e "source a:\\xxx\\yyy.sql"'
或
start-process -FilePath 'cmd.exe' -ArgumentList ' /c a:\\mysql5.6\\bin\\mysql.exe -uroot < a:\\xxx\\yyy.sql'

代替大于号的方法是，用-r。详见mysql手册。
http://dev.mysql.com/doc/refman/5.7/en/mysqldump.html
mysqldump.exe -uroot --result-file=a:\\xxx\\yyy.sql -B 库名
--result-file=file\_name, -r file\_name

------------------【展开的讨论】------------------

问：mysqldump是什么？
答：
导出数据库，生成sql源码文本。
通过叫通 ip/端口/用户名/密码，来查询服务端口，得到字符串，处理字符串，然后写入文本。
这里要说明的是，实际上是一次产生n行，写入内存缓存，（受my.ini中，[mysqldump]字段中，max\_allowed\_packet = xxM参数控制）然后追
加入文件。并不是一行一追加。况且一行一追加，并没有意义。和二进制追加写入没区别。

问：mysql.exe < xxx.sql是什么？
答：
载入文本并提交给服务器端口。
命令行载入文本，都必须用【<】号了么？为什么Get-Content不用？

问：win，linux命令行中，程序通用的参数的写法，应该是什么？
答：
命令名 -参数名 参数值
命令名 --参数名 参数值
或
命令名 /参数名 参数值

powershell 传教士 原创文章 始作于 2016-02-19 允许转载，但必须保留名字和出处，否则追究法律责任

问：powershell中【<】取消了么？
答：
是

问：假设powershell中【>】取消了，那我用什么？
答：
用
out-file -InputObject \$要输出的内容 -FilePath 文件名
https://technet.microsoft.com/en-us/library/hh849882.aspx

或用
Set-Content -Value \$要输出的内容 -Path 文件名
https://technet.microsoft.com/en-us/library/hh849828.aspx

问：假设powershell中【>>】取消了，那我用什么？
答：
用
Add-Content -Value \$要输出的内容 -Path 文件名
https://technet.microsoft.com/en-us/library/hh849859.aspx

注意：上述ps命令支持管道，支持单行，支持多行，支持换行和不换行。

------------------【其他数据库是如何做的？】------------------

让我们先看看，其他数据库的做法。

oracle：
E:\\>exp system/oracle FILE=c.dmp FULL=Y

sqlserver + sqlcmd：
恢复
sqlcmd -S ./sqlexpress -i c:/createdata.sql

sqlserver + powershell：
在SQL Server 2012 中,微软增加了4个新的备份和还原的cmdlet
Backup-SqlDatabase
Restore-SqlDatabase
Backup-ASDatabase
Restore-ASDatabase

结论：
这些数据库软件的，导出sql命令，导入sql命令，都没有用到【<】，【>】，【>>】这些符号。

# ------------------【引发的思考】------------------

1 这应不应该算mysql客户端程序不合理的设计？bug？

2 应不应该给mysqldump.exe 添加 --outfile 之类的参数来代替【>】？
答：
已经有了，是-r参数。

3 应不应该给mysql.exe 添加 --runsqlfile 之类的参数来代替【<】？
既然上面的可以不用大于号，为什么这个命令没有，不用小于号的参数？

4 码农是干嘛的？

程序员（码农）是虚拟世界的创世神，符合逻辑的要长处来，不符合逻辑的应砍掉。虚拟世界的规则是创世神规定的，
虚拟世界中，路人甲砍路人乙一刀，乙是掉脑袋，还是额外长出个脑袋，应该被创世神来规定。样虚拟世界才能茁壮成长。
当然不能胡乱规定，否则虚拟世界将坍塌。
如何定义规则，是费脑筋的。否则要架构师干嘛？当然细节码农也要参与。

如果你是一只猴子，我要抢走你【<】，【>】，【>>】牌香蕉，你会很不满意的大吼么？
到底要不要夺走猴子的这只香蕉？？？

------------------【完】------------------

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [powershell传教士](https://www.cnblogs.com/piapia/tag/powershell%E4%BC%A0%E6%95%99%E5%A3%AB/) , [脚本](https://www.cnblogs.com/piapia/tag/%E8%84%9A%E6%9C%AC/) , [mysql](https://www.cnblogs.com/piapia/tag/mysql/) , [mysqldump](https://www.cnblogs.com/piapia/tag/mysqldump/)

