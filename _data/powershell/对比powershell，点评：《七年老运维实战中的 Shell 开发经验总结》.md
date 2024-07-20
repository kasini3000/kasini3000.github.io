# 前言：

《七年老运维实战中的 Shell 开发经验总结》是不错的帖子，建议shell用户搜看。

# 点评：《七年老运维实战中的 Shell 开发经验总结》

![](https://img2020.cnblogs.com/blog/456691/202107/456691-20210702104344103-654105975.jpg)

shell除了上述问题外，还有没有对象的问题。

# python是shell的救星吗？

python对于运维来讲，有3个癌症：1用命令前需要import库。2没有shell级别命令行。3管道用起来太麻烦。
可以简单把ps理解为没有这三个癌症的python。
1powershell的自动import太好用，99.9%不用手动import。即便命令函数重名，也可以用库名/命令名（函数名）来解决。如：Microsoft.PowerShell.Management\\Get-ChildItem
3写py【管道】脚本，需要import，open，read，close。而powershell，bash，使用管道数据，不需要这些步骤。
假如py消掉了这三个癌症，shell就没啥必要存在了。这就是powershell比py，比shell的优势。

# 结论：

大部分shell的经验，不适合于powershell，乃因为powershell是高级脚本功法。
powershell具有shell和python，这两家共同的长处。

谢谢观看，欢迎提供不同意见，欢迎交流。powershell 群：183173532

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [脚本](https://www.cnblogs.com/piapia/tag/%E8%84%9A%E6%9C%AC/) , [shell](https://www.cnblogs.com/piapia/tag/shell/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [linux](https://www.cnblogs.com/piapia/tag/linux/)
