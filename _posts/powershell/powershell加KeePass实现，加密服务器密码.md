powershell加KeePass实现，加密服务器密码

powershell传教士翻译，改写。 2020-02-27

原文： https://www.altaro.com/msp-dojo/keepass/

# 前言：

powershell自己，已经实现加密密码保存，读取。功能全自动。
但是捏，【全自动加密、解密】基本等于【明文保存密码】。记住，这种方法是掩耳盗铃！！！你能自动解密，黑客也能。

而用一个【每次运行，都必须手动输入的主密码】，管理n个子密码，才是最好的对称加密方法。
或者用【非对称，公私钥，秘钥对，加密】技术。

所以你要么掩耳盗铃，要么麻烦输入密码。 世界上没有又安全又简单的技术，只有安全但麻烦，或简单但不安全的技术。

# 简介：

KeePass是开源+免费的软件。

KeePass官方网站：
https://keepass.info/download.html

KeePass的作用是，用一个手动输入的，无法保存的主密码，管理所有自动输入的，子用户名，密码，标签。
这里讲的主要是powershell的PoShKeePass模块。

# 安装：

Install-Module -Name PoShKeePass

# 查看KeePass数据库：

以.kdbx为文件扩展名
Get-KeePassDatabaseConfiguration

# 连接KeePass数据库：

New-KeePassDatabaseConfiguration \`
-DatabaseProfileName 'LukeLab'  \`
-DatabasePath "C:\\Temp\\KeePass\\Database.kdbx" -UseMasterKey

# 生成一个密码字串：

\$password = New-KeePassPassword -UpperCase -LowerCase -Digits -SpecialCharacters -Length 10

# 在数据库中，新建用户名+密码条目：

New-KeePassEntry -DatabaseProfileName LukeLab -Title 'ESXi1' -UserName root -KeePassPassword \$password -KeePassEntryGroupPath Database/ESXi
-Title 指定目录

# 获取，用户名+密码条目：

\$KeePassEntry = Get-KeePassEntry -KeePassEntryGroupPath Database/ESXi -Title "ESXi1" -DatabaseProfileName "LukeLab"
\$KeePassEntry.username #账户
\$KeePassEntry.password #密码

# 在数据库中，更新用户名+密码条目：

Update-KeePassEntry -Title 'ESXi1' -UserName root -KeePassPassword (New-KeePassPassword -UpperCase -LowerCase -Digits -SpecialCharacters -Length 10) -KeePassEntryGroupPath Database/ESXi -KeePassEntry \$KeePassEntry -DatabaseProfileName 'LukeLab'

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [keepass](https://www.cnblogs.com/piapia/tag/keepass/) , [密码](https://www.cnblogs.com/piapia/tag/%E5%AF%86%E7%A0%81/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)
