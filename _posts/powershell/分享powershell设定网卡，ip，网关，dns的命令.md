# -------------------------------------------------【系统需求】-------------------------------------------------

不支持win7，win2008r2

支持win8及以上

支持win2012及以上。

# -------------------------------------------------【获取网卡】-------------------------------------------------

获取所有网卡：
Get-NetAdapter

获取单个网卡：
Get-NetAdapter -Name "Ethernet0" #或以太网，
或:
Get-NetAdapter -InterfaceIndex 8
================================

获取网卡物理信息
Get-NetAdapterHardwareInfo

# -------------------------------------------------【获取ip，网关等】-------------------------------------------------

Get-NetIPConfiguration -InterfaceAlias Ethernet0 -Detailed#或以太网，

#获取ip
(Get-NetAdapter -Name ethernet0 | Get-NetIPAddress).IPv4Address# -------------------------------------------------【重启网卡】-------------------------------------------------

Restart-NetAdapter -InterfaceAlias Ethernet0 #或以太网

# -------------------------------------------------【设定ip，网关】-------------------------------------------------

在远程win2019上，更改ip。更改成功后会卡死，需要重新用新ip连接win2019。
(Get-NetAdapter)[-1] | New-NetIPAddress -AddressFamily IPv4 -IPAddress 192.168.11.220 -PrefixLength 8 -DefaultGateway 192.168.11.1

#更改ip
(Get-NetAdapter)[-1] | Set-NetIPAddress  -IPAddress 192.168.11.90

#设定成从dhcp获取
(Get-NetAdapter)[-1] | Set-NetIPInterface -Dhcp Enabled

================================================================================================================
禁用ipv6
(Get-NetAdapter)[-1]| Disable-NetAdapterBinding -ComponentID ms\_tcpip6

# -------------------------------------------------【设定dns】-------------------------------------------------

(Get-NetAdapter)[-1] | Set-DNSClientServerAddress -ServerAddresses 192.168.11.1,114.114.114.114

#自动获取dns
(Get-NetAdapter)[-1] |Set-DnsClientServerAddress -ResetServerAddresses# -------------------------------------------------【机子改名】-------------------------------------------------

Rename-Computer -ComputerName . -NewName 'ps传教士2020' #这条命令不支持中文机子名，垃圾。

(Get-WmiObject win32\_computersystem).rename('ps传教士2021') #good

# -------------------------------------------------【设定路由】-------------------------------------------------

添加：
New-NetRoute -DestinationPrefix "0.0.0.0/0" -NextHop "192.168.12.2" -InterfaceIndex 8

删除：
Set-NetIPInterface -InterfaceAlias Ethernet0| Remove-NetRoute -Confirm:\$false

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [dns](https://www.cnblogs.com/piapia/tag/dns/) , [网关](https://www.cnblogs.com/piapia/tag/%E7%BD%91%E5%85%B3/) , [网卡](https://www.cnblogs.com/piapia/tag/%E7%BD%91%E5%8D%A1/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [ip](https://www.cnblogs.com/piapia/tag/ip/)
