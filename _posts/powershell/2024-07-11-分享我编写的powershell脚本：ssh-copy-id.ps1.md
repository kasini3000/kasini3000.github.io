





# 分享我编写的powershell脚本：ssh-copy-id.ps1




问：通过【字符串界面】。如何从win，通过ssh，连接到sshd？
答：
在任意版本win中，通过cmd.exe，powershell.exe中调用ssh.exe，连接sshd。

问：通过【powershell对象界面】。如何从win，通过ssh，连接到sshd？
答：
ssh客户端，需要安装powershell6.0，及以上。
ssh服务器端，需要改写sshd_config，加上
Subsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile
并重启sshd。
powershell传教士 2019-02-20

问：通过powershell对象界面。如何从win，通过ssh，连接到sshd？poweershell代码是什么？
答：
\$连接1 = New-PSSession -HostName 1.1.1.1 -UserName root
invoke-command -ScriptBlock { xx命令 } -Session \$连接1
#或 invoke-command -FilePath 客户机上的脚本\_在服务器上运行.ps1  -Session \$连接1

问：上面命令每次都需要输入密码么？
答：
对，powershell团队永远不会开发保存密码的功能。因为那是低安全级别。

问：
为什么要用new-pssession，不用ssh.exe？
powershell对象界面，比字符界面有哪些好处？
服务器端，客户端，都用powershell有啥好处？
答：
1 服务器端<--->客户端之间，传递对象变量，而无需序列化/反序列化。
2 自动从客户端，传递脚本文件，到服务器端。如：
invoke-command -FilePath 客户机上的脚本\_在服务器上运行.ps1  -Session \$连接1

问：如何实现ssh免密？
答：
1 使用无密码ssh秘钥对。ssh秘钥比ssh密码长度长，更安全。还支持服务端，客户端双向认证。
或
2 使用有密码ssh秘钥对+【ssh-agent.exe】
注意：这里的ssh秘钥对，适合于ssh.exe连接sshd，也适合于new-pssession连接sshd。

问：怎么生成ssh秘钥对？
答：
1 ssh-keygen.exe回车，可以加参数。
2 使用puttygen.exe。
3 使用xshell ---》工具 ---》新建 用户秘钥生成向导。
等。

问：用私钥，通过powershell对象界面。如何从win，通过ssh，连接到sshd？poweershell代码是什么？
答：
\$连接1 = New-PSSession -HostName 1.1.1.1 -UserName root -KeyFilePath \$env:USERPROFILE\\.ssh\\id\_rsa
invoke-command -ScriptBlock { xx命令 } -Session \$连接1
#或 invoke-command -FilePath 客户机上的脚本\_在服务器上运行.ps1  -Session \$连接1

问：ssh-copy-id.ps1脚本的作用是？
答：
把ssh秘钥的【公钥】从win、linux本机，复制到目标ssh服务器。
不用输入密码，从而达到ssh免密自动化。
注意：这里的ssh秘钥对，适合于ssh.exe连接sshd，也适合于new-pssession连接sshd。

问：去哪里下载win版ssh服务器，客户端？
答：
https://github.com/PowerShell/Win32-OpenSSH/releases

问：为什么说我编写的ssh-copy-id.ps1，比linux的ssh-copy-id还好用？
答：
linux的ssh-copy-id，对每台目标sshd服务器，都需要手动输入密码。不适合于批量操作。
而我编写的脚本，可以保存密码。你也可以改写脚本，传入密码。
注意：不是保存【远程连接】的密码，而是公钥部署，使用事先保存的密码！

问：ssh-copy-id.ps1脚本的使用场合是？
答：
从win客户端，向linux服务器，推送ssh-pub-keyfile

问：运行ssh-copy-id.ps1有什么前提要求（系统需求）？
答：
1 至少powershell 5.0。win7请直接安装ps5.1：
https://docs.microsoft.com/zh-cn/powershell/wmf/5.1/install-configure
win10跳过此步骤。

2 （可选）需要用管理员权限，开启powershell.exe，运行install-module powershellget -Force

3 （必选）需要用管理员权限，开启【一个新的！】powershell.exe，运行install-module winscp

对于psv5，psv7，需要分别运行install-module winscp

注意：'/root/.ssh/authorized\_keys'此文件将被覆盖，而不是插入新的值。

```
脚本最新版下载地址：
https://pan.baidu.com/s/16deKKe3ZnCg809lffiVZWg

或

https://gitee.com/chuanjiao10/powershell/tree/master/win
```

ssh-copy-id6.ps1

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
# 免责声明：使用本脚本带来的一切不良后果，本人概不负责！

<#
脚本目的：
从win、linux中复制【本机ssh公钥】，到【目的linux版ssh服务器】。

前提条件：
install-module winscp

用法：
ssh-copy-id6.ps1 -目的ip 1.2.3.4 -用户密码明文 '你的密码明文'
建议保存编码为：bom头 + utf8

脚本最新版下载地址：
https://pan.baidu.com/s/16deKKe3ZnCg809lffiVZWg

#>

param
(
    $目的ip = '192.168.1.2',
    $用户密码明文 = '这里填入你的ssh密码明文作为默认值，或在命令行输入参数'
)

#先运行ssh-keygen回车，产生key文件。https://github.com/PowerShell/Win32-OpenSSH/releases
if (($PSEdition -eq 'Desktop') -or (test-path c:\))
{
    $key文件1 = "$env:USERPROFILE\.ssh\id_rsa.pub"
    $key文件2 = "$env:USERPROFILE\.ssh\authorized_keys"
    Copy-Item -LiteralPath $key文件1  -Destination $key文件2
}

$用户名 = 'root'

$用户密码密文 = ConvertTo-SecureString $用户密码明文 -AsPlainText -Force
$我的登陆凭据 = New-Object System.Management.Automation.PSCredential ($用户名,$用户密码密文)
#QQ群号=183173532,名称=powershell交流群，2019-06-13
$sftp连接参数 = new-WinSCPSessionOption -Protocol Sftp -HostName $目的ip  -Credential  $我的登陆凭据
$指纹 = Get-WinSCPHostKeyFingerprint -SessionOption $sftp连接参数 -Algorithm SHA-256
$sftp连接参数.SshHostKeyFingerprint = $指纹
$sftp连接 = new-WinSCPSession -SessionOption $sftp连接参数

if (Test-WinSCPPath -Path '/root/.ssh' -WinSCPSession $sftp连接)
{
    Remove-WinSCPItem -Path '/root/.ssh' -Confirm:$false -WinSCPSession $sftp连接
}

$权限700 = New-WinSCPTransferOption -FilePermissions (New-WinSCPItemPermission -Octal 700)
New-WinSCPItem -Path '/root/.ssh' -ItemType Directory -TransferOptions  $权限700 -WinSCPSession $sftp连接

$权限600 = New-WinSCPTransferOption -FilePermissions (New-WinSCPItemPermission -Octal 600)
Send-WinSCPItem -LocalPath $key文件2 -RemotePath '/root/.ssh/' -TransferOptions $权限600 -WinSCPSession $sftp连接

Remove-WinSCPSession -WinSCPSession $sftp连接
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [ssh-copy-id](https://www.cnblogs.com/piapia/tag/ssh-copy-id/) , [id\_rsa.pub](https://www.cnblogs.com/piapia/tag/id_rsa.pub/) , [authorized\_keys](https://www.cnblogs.com/piapia/tag/authorized_keys/) , [ssh秘钥](https://www.cnblogs.com/piapia/tag/ssh%E7%A7%98%E9%92%A5/) , [ssh](https://www.cnblogs.com/piapia/tag/ssh/) , [keyfile](https://www.cnblogs.com/piapia/tag/keyfile/)

