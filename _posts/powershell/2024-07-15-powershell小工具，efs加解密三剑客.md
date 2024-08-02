---
categories: powershell
layout: post
permalink: /powershell/powershell小工具，efs加解密三剑客。
---


两款 efs 加密，解密，powershell脚本工具。1 列出所有efs加密文件。2解密所有efs加密文件。

powershell  efs  加密  解密  列出  decryption  list

使用前提：

网管会破解当前机子上的，win账户密码，可以用当前win账户登录。

因为这个账户中保存着win ntfs efs文件解密秘钥。如果这台机子重装了，则efs解密秘钥不存在了。

所以应该在重装前，登录员工win上的账户，然后运行我写的脚本。

脚本应该用普通用户权限执行，无需管理员权限，扫描一些系统目录时会报错，无法读取，真是正常的。不用理会。

--------前言：我编写这两个脚本的目的，大家不可不知！--------

１　是为系统管理员，网管员编写的工具。
某员工离职后，他的磁盘假设有一万个文件（目录），其中efs加密的文件目录有3个。资源管理器中，这三个文件是绿色的。
但你要是（一万个目录）挨个点开看，是不是绿色，要累死。
这时网管用员工的win账户，登录员工ｐｃ机，用此脚本，即可列出所有ｅｆｓ加密文件。
当然前提要有ntfs读（目录文件）的权限。在一定程度上防止离职员工破坏数据。

2    请看这个场景：对于用护自己来说，用户磁盘目录中的项目。某些文件，是加密的。某些是没加密的。有些时候需要复制并解密某个目录，把这个目录整个给客户。那么就需要这两个脚本了。

注意：看到这里有人可能要说了，把整个目录复制到fat32分区不就得了？这样就把整个efs目录自动全部解密了。

我可以告诉你，这种方法对于win10不灵了。很多人用win10，win10下，fat32也是支持efs加密的。你的招数不灵了。

也就是说，你要把你的文件，复制到别人移动硬盘后，再运行我写的软件，给移动硬盘上的efs目录解密了。

--------列出盘符，或目录下所有efs文件--------

![](https://images0.cnblogs.com/blog2015/456691/201508/041711023766361.png)

--------解密盘符，或目录下所有efs文件--------

![](https://images0.cnblogs.com/blog2015/456691/201508/041711164087707.png)

上述脚本下载地址：

[https://files.cnblogs.com/files/piapia/efs\_powershell.zip](https://files.cnblogs.com/files/piapia/efs_powershell.zip)

上述只有二剑客，下述还有一剑客，合成三剑客。

powershell efs-copy 软件

http://www.cnblogs.com/piapia/archive/2013/01/09/2853229.html

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [efs](https://www.cnblogs.com/piapia/tag/efs/) , [加密](https://www.cnblogs.com/piapia/tag/%E5%8A%A0%E5%AF%86/) , [解密](https://www.cnblogs.com/piapia/tag/%E8%A7%A3%E5%AF%86/) , [列出](https://www.cnblogs.com/piapia/tag/%E5%88%97%E5%87%BA/) , [decryption](https://www.cnblogs.com/piapia/tag/decryption/) , [list](https://www.cnblogs.com/piapia/tag/list/)


