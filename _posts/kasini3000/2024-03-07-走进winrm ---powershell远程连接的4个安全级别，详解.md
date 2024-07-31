# ---------【winrm的“四级”安全】---------

四级安全，就是最不安全的。

winrm默认使用http+5985端口，密码传输加密，数据、命令传输明文。
有被人窃取机密，和插入攻击命令的风险，也就是所谓的中间人攻击。不过呢，其实问题不大。
解决的话，启用https版的winrm，或者在http外面套上vpn即可。

强调一遍：win2012r2，win2016，win2019默认开启这种http的5985端口。

# ---------【winrm的“三级”安全】---------

三级安全，是指给http套上ssl外衣。

给http套上ssl外衣后，传输内容加密了，连接过程还是需要认证用户名和密码。
这就是我说的【赖法，强制启动，https版的winrm】，也就是说winrm客户端连接winrm服务器使用ssl，但跳过自签名证书，跳过域名绑定，我觉得这样足够了。

跳过证书，跳过域名绑定，的赖法，靠这两个法宝。
-SkipCACheck #跳过证书
-SkipCNCheck #跳过服务器，机器名

## winrm服务器：

#用管理员powershell：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
Get-childitem WSMan:\Localhost\listener\
Remove-Item -Path WSMan:\Localhost\listener\listener* -Recurse
$winrm证书 = New-SelfSignedCertificate -CertstoreLocation Cert:\LocalMachine\My -DnsName 'powershell交流群183173532'
Get-childitem WSMan:\Localhost\listener\
New-Item -Path WSMan:\LocalHost\Listener -Transport HTTPS -Address *  -Force -CertificateThumbPrint $winrm证书.Thumbprint
Get-ChildItem Cert:\LocalMachine\My |Where-Object Thumbprint -eq $winrm证书.Thumbprint |Remove-Item  -Force
#防火墙添加5986端口
New-NetFirewallRule -DisplayName "ps传教士winrm的https in" -Name "Windows Remote Management (HTTPS-In)" -Profile Any -LocalPort 5986 -RemoteAddress Any -Protocol TCP
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

## winrm客户机：

#此命令要求输入，winrm服务器上的账户密码

```
Invoke-Command -ComputerName $winrm服务器ip -Port 5986 -Credential (Get-Credential) `
-UseSSL -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck) `
-ScriptBlock { dir c:\ }
```

---------【winrm的“二级”安全】---------

二级安全，是指在赖法的基础上。winrm服务器/winrm客户机连接，使用https自签名证书。

需要在winrm服务器上，建立一对秘钥，放在【Cert:\\LocalMachine\\my】里。没错，服务器上必须有公钥+私钥。
winrm客户机上只需要公钥。把公钥导入，放到客户机的【Cert:\\LocalMachine\\root】里。

连接时，winrm客户机用公钥，winrm服务器上用私钥，这一点和ssh相反。winrm客户机公钥泄露问题不大，因为同时还必须要有winrm服务器的账户和密码才能登陆。

winrm证书要求:
设置证书增强型密钥使用 (EKU)“服务器身份验证”(OID=1.3.6.1.5.5.7.3.1)。
将“证书主题”设置为“CN=HOSTNAME”。

winrm服务器：

#管理员权限powershell，在winrm服务器上，在【证书-本地计算机-》个人-》证书】中，建立证书：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$winrm证书参数 = @{
CertStoreLocation = "Cert:\LocalMachine\My"
KeyAlgorithm = 'RSA'
HashAlgorithm = 'sha256'
KeyLength = 4096
Subject = "CN=powershell交流群183173532"
NotBefore = (get-date) - [timespan]::FromDays(365)
NotAfter = (get-date) + [timespan]::FromDays(3650)
}

$winrm证书 = New-SelfSignedCertificate @winrm证书参数
#在winrm客户机，只导出证书公钥：
Export-Certificate -Cert $winrm证书 -FilePath 'd:\winrm证书公钥.cer'

#让winrm监听6516端口：默认端口=5985(http)，不监听5986(https)
Get-childitem WSMan:\Localhost\listener\
Remove-Item -Path WSMan:\Localhost\listener\listener* -Recurse
Get-childitem WSMan:\Localhost\listener\
New-Item -Path WSMan:\LocalHost\Listener -Transport HTTPS -Address *  -Force -CertificateThumbPrint $winrm证书.Thumbprint

防火墙开启，winrm服务器6516端口：
New-NetFirewallRule -DisplayName "ps传教士winrm的https in" -Name "Windows Remote Management (HTTPS-In)" -Profile Any -LocalPort 5986 -RemoteAddress Any -Protocol TCP
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

## winrm客户机：

复制公钥文件'd:\\winrm证书公钥.cer'，到客户机上。

到winrm客户机上，导入公钥到【受信任证书颁发机构-》证书】：

管理员powershell：

```
Import-Certificate -Filepath 'd:\winrm证书公钥.cer' -CertStoreLocation "Cert:\LocalMachine\root"
```

然后从winrm客户机，连接winrm服务器，就不能跳过ca了。

请看代码：

```
#在winrm客户机上运行：此命令要求输入，winrm服务器上的账户密码
Invoke-Command -ComputerName $winrm服务器ip -Port 5986 -Credential (Get-Credential) `
-UseSSL -SessionOption (New-PSSessionOption -SkipCNCheck) `
-ScriptBlock { dir c:\ }
```

---------【winrm的“一级”安全】---------

一级安全，是指在二级的基础上，从winrm客户端，校验winrm服务端证书的cn。这里的cn名，类似于域名。它是服务器的计算机名【\$env:COMPUTERNAME】

## winrm服务器：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$winrm证书参数 = @{
CertStoreLocation = "Cert:\LocalMachine\My"
KeyAlgorithm = 'RSA'
HashAlgorithm = 'sha256'
KeyLength = 4096
Subject = "CN=$env:COMPUTERNAME"
NotBefore = (get-date) - [timespan]::FromDays(365)
NotAfter = (get-date) + [timespan]::FromDays(3650)
}

$winrm证书 = New-SelfSignedCertificate @winrm证书参数

#在winrm客户机，只导出证书公钥：
Export-Certificate -Cert $winrm证书 -FilePath 'd:\winrm证书公钥.cer'

#让winrm监听6516端口：默认端口=5985(http)，不监听5986(https)
Get-childitem WSMan:\Localhost\listener\
Remove-Item -Path WSMan:\Localhost\listener\listener* -Recurse
Get-childitem WSMan:\Localhost\listener\
New-Item -Path WSMan:\LocalHost\Listener -Transport HTTPS -Address *  -Force -CertificateThumbPrint $winrm证书.Thumbprint

#防火墙开启，winrm服务器6516端口：
New-NetFirewallRule -DisplayName "ps传教士winrm的https in" -Name "Windows Remote Management (HTTPS-In)" -Profile Any -LocalPort 5986 -RemoteAddress Any -Protocol TCP
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

## winrm客户机：

复制公钥文件'd:\\winrm证书公钥.cer'，到客户机上。

到winrm客户机上，导入公钥到【受信任证书颁发机构-》证书】：

管理员powershell：

```
Import-Certificate -Filepath 'd:\winrm证书公钥.cer' -CertStoreLocation "Cert:\LocalMachine\root"
```

然后从winrm客户机，连接winrm服务器，就不能跳过ca，也不能跳过cn了。请看代码：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$PSRemoting服务器ip = 'DESKTOP-XXXX' #若ping DESKTOP-XXXX返回的ip不对，则要在host中添加ip 计算机名的映射。
$PSRemoting服务器用户名 = 'abcd'
$用户名 = "$PSRemoting服务器ip\$PSRemoting服务器用户名"
$密码明文 = '1234'
$密码密文 = ConvertTo-SecureString $密码明文 -AsPlainText -Force
$用户名和密码捆绑后的授权信息 = New-Object System.Management.Automation.PSCredential ($用户名,$密码密文)
$连接1 = New-PSSession -ComputerName $PSRemoting服务器ip -Port 5986 -Credential $用户名和密码捆绑后的授权信息 -UseSSL
Invoke-Command -session $连接1 -ScriptBlock {dir d:\}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# ---------【winrm的“最”安全】---------

最安全当然是，不用自签名证书，购买证书了。
或者加入win的域控，成为域内机子。
从win中的powershell，连接到linux版powershell，使用ssh非对称秘钥，也很安全。它是服务器，客户机双向验证。

# ---------【后记】---------

我写这篇帖子的目的是，给winrm启用https。因为winrm+https也可以做ps+web的后端，比如最微软著名的web运维框架“火奴撸撸”。

谢谢观看

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [winrm](https://www.cnblogs.com/piapia/tag/winrm/) , [https](https://www.cnblogs.com/piapia/tag/https/) , [自签名证书](https://www.cnblogs.com/piapia/tag/%E8%87%AA%E7%AD%BE%E5%90%8D%E8%AF%81%E4%B9%A6/) , [5986](https://www.cnblogs.com/piapia/tag/5986/)
