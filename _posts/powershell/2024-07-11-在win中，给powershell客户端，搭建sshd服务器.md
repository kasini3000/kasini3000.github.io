









# 在win中，给powershell客户端，搭建sshd服务器


**下载**：
https://github.com/PowerShell/Win32-OpenSSH/releases

**问：为什么要用这个sshd？**
答：
这是微软用，openssh官方的源码，
源码网址：
https://github.com/PowerShell/openssh-portable
被微软编译后的2进制程序。2进制程序上，有微软的签名。

win下，免费的sshd，是不是微软这个版本最新？并且开放源码？
不建议使用其他公司的闭源sshd，里面可能有后门。
而且收费的sshd，虽然软件比较新，但一般都是闭源的。
不建议使用，n年前的低版本sshd，软件太老有漏洞。

**解压**：可以解压到任意目录。
建议解压到：
d:\\OpenSSH\_Win64\_7.9p1
不建议安装在c盘。

**安装**：
powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1

服务名为：
sshd

显示服务名为：
OpenSSH SSH Server
powershell 传教士 原创文章 ，2019-04-23

**sshd\_config配置文件存储目录为**：
\$env:ProgramData\\ssh\\
或
C:\\ProgramData\\ssh\\

目的：
**生成sshd\_config等文件**
动作：
启动并停止服务：
Set-Service -Name sshd -StartupType 'Automatic'
start-service sshd
stop-service sshd

管理员权限的powershell，**更换sshd服务器的，主机秘钥**：
Remove-Item -path C:\\ProgramData\\ssh\\ssh\_host\_\*
cd d:\\你的安装目录
ssh-keygen -A

注意：更换主机秘钥后，客户机需要删除，用户的known\_host文件，如： /root/.ssh/known\_hosts

**问：如何更改sshd\_config？**
答：
这个文件存储在c盘，修改后保存不了。所以你应该
复制C:\\ProgramData\\ssh\\sshd\_config到d盘。
用记事本打开并更改，保存。
再复制回来。
这时文件权限坏了，但并不影响sshd服务启动。

**问：ssh客户端公钥，添加到哪里？**
答：
此版本的ssh客户端公钥，应该添加到这个文件中：
C:\\ProgramData\\ssh\\administrators\_authorized\_keys

**用管理员权限的powershell，开启防火墙22端口**：
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22

用管理员权限的powershell**，让powershell成为默认shell**：
New-ItemProperty -Path "HKLM:\\SOFTWARE\\OpenSSH" -Name DefaultShell -Value "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" -PropertyType String -Force

用管理员权限的powershell，**让powershell把远程发送来的字符串，识别为命令，而不是脚本名**：
New-ItemProperty -Path "HKLM:\\SOFTWARE\\OpenSSH" -Name DefaultShellCommandOption -Value "/c" -PropertyType String -Force

目的：**让（win，linux）ssh服务器，接收来自客户机【powershell的连接】**：
管理员权限运行powershell后，运行命令：

win，linux，客户机中的powershell--->ssh--->win服务器上的的sshd：

**1下载**：
https://github.com/PowerShell/PowerShell/releases

**2安装**：**必须安装powershell6。powershell5.1不行。**
如PowerShell-6.2.0-win-x64.msi

3目的：由于sshd不支持带空格的路径，所以要给它**建立一个链接目录**。
New-Item c:\\pwsh -ItemType SymbolicLink -Target 'C:\\Program Files\\PowerShell\\6'

4目的：在sshd\_config的非最后面行中，**写入subsystem**。在sshd\_config文件最后面写入subsystem，则sshd启动不了。opensshd真是奇葩啊！

用管理员权限的powershell，在win服务器上的sshd上运行：

```
$f = "$env:ProgramData\ssh\sshd_config"
$f2 = Get-Content -LiteralPath $f
Set-Content -LiteralPath $f -Value $f2[0..7]
Add-Content -LiteralPath $f -Value 'Subsystem powershell c:\pwsh\pwsh.exe -sshs -NoLogo -NoProfile'
Add-Content -LiteralPath $f -Value $f2[8..$f2.length]
```

win，linux，客户机中的powershell--->ssh--->**linux服务器上的的sshd**：
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

**重新启动win上的sshd服务**：
restart-service sshd

**问：从linux经ssh连接到win上的sshd，为什么要用powershell？而不是bash？**
**问：powershell经过ssh，远程运行命令，比bash强在哪？**

答：
=============linux远程命令 & ps远程命令 对比例子============
任意sh ---> ssh ---> linux上的bash：
ssh aaa@1.1.1.1 "以用户aaa权限执行的，命令xxx"

win，linux中的powershell ---> ssh ---> linux上的powershell：
[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$a = 1
[scriptblock]$备份命令 =
{
 Get-Date
 $b = $using:a + 2 #引用客户机变量，需要用$using:
}
$连接1 = New-PSSession -HostName 1.1.1.1 -UserName root  #手动输入密码或用-KeyFilePath 选项。还需要修改/etc/ssh/sshd_config
invoke-command -ScriptBlock { $备份命令 } -Session $连接1
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

============================================================
bash的远程命令，简单直接。就好像我左手这盘蛋炒饭，简单解饿，但是不够强。更适用于 简单远程命令场合。
你再看看我右手这盘盖饭好在哪？答：生菜垫底，萝卜雕花围边。
bash远程传递的是【字符串】，powershell传递的是【代码块】。特色是【对象垫底，大花括号围边】。

字符串传递到远程时，经常需要要转义。代码块不用。
代码块，支持多行，格式化，使代码美观。
变量名，函数名支持中文。

代码块中，支持引用客户端变量，一律加上【\$using:】，即客户机上的【\$a】,在服务器上叫【\$using:a】
代码块中，支持引用服务器端变量，即服务器上的【\$a】,在服务器还上叫【\$a】
即使变量重名，两个\$a也绝不会弄混。
强类型变量，在远程使用，无需序列化/反序列化。这点py不行。java不行。

ps用大花括号包围代码，不用单双引号，代码嵌套很容易。
而代码嵌套容易，使的ps的ssh远程，从server1（跳板机，堡垒机）经ssh进入server2，再ssh进入server3，进入33层ssh server执行命令很容易。而shell难。

远程代码天生不老稳定的，有时没反应，或卡住，或中途断了。遇到此情形，每行ps代码都可以在外面套上try-cacth，比shell更稳。

批量ssh，ps采用【多线程】，比bash用【多进程】快，时间准，省内存。

**----------【测试】----------**
先在win中的d盘，建立几个中文文件名。

从终端软件xshell中，连接：
win用户名
win密码
终端软件的编码，选unicode（utf-8），这点和连接linux相同。

从终端软件xshell中，发送测试命令：
\$PSVersionTable
get-childitem d:\\
dir 查看中文会不会乱码。

至此，win服务器安装完毕。

从客户机的powershell中，发送测试命令：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$服务器ip = '192.168.1.201'
$账户名 = 'xxxx'
[scriptblock]$测试命令 =
{
 get-childitem d:\
}
$连接2 = New-PSSession -HostName $服务器ip -UserName $账户名
#这里将提示你输入密码
$要返回的内容 = Invoke-Command -ScriptBlock $测试命令 -Session $连接2
$要返回的内容
$要返回的内容.name  # dir 返回的文件名
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

问：**powershell的New-PSSession能把密码写在脚本中么**？
答：
不能。

问：**ssh如何免密？**
答：
使用用户私钥文件。
\$连接3 = New-PSSession -HostName \$远程ip -UserName \$账户名 -keyfile c:\\Users\\userabcd\\.ssh\\id\_rsa

**问：ssh如何复制，用户公钥到服务器？**
答：
https://www.cnblogs.com/piapia/p/10412550.html

**客户机本地的ps1脚本，放在远程ssh+powershell服务器上执行**：
在客户机上：
Invoke-Command -FilePath d:\\abc.ps1 -Session \$连接4

**客户机本地的命令，放在远程ssh+powershell服务器上执行**：
在客户机上：
Invoke-Command -ScriptBlock \$测试命令 -Session \$连接5

**在远程sshd+powershell服务器上执行，命令，脚本**：
在客户机上：
Invoke-Command -ScriptBlock { get-date; d:\\xxx.ps1 ;#或 /tmp/a.ps1 } -Session \$连接6

**问：如何scp文件？**
答：
scp /tmp/abc.tar.gz   your\_name@ip:d:\\dir123
openssh官方现在不建议使用scp了。建议使用sftp。

**问：如何sftp文件？**
答：
用powershell的winscp第三方模块。
开源免费软件，支持图形，也支持命令行。win，linux通用。
install-module winscp

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [win](https://www.cnblogs.com/piapia/tag/win/) , [windows](https://www.cnblogs.com/piapia/tag/windows/) , [sshd](https://www.cnblogs.com/piapia/tag/sshd/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [subsystem](https://www.cnblogs.com/piapia/tag/subsystem/) , [ssh](https://www.cnblogs.com/piapia/tag/ssh/)







