---
categories: k4t2
layout: post
permalink: /k4t2/powershell加win的dns服务器，解决网站负载均衡问题
---

# ------------------------win2012r2中，用powershell管理dns服务器的，命令列表--------------------------------

共计100条指令，即100招，下面演示如何用其中4招秒杀bind的。

PS C:\\Users\\admin> get-command \* -module DnsServer

CommandType Name ModuleName

---

Alias Export-DnsServerTrustAnchor DnsServer
Function Add-DnsServerConditionalForwarderZone DnsServer
Function Add-DnsServerDirectoryPartition DnsServer
Function Add-DnsServerForwarder DnsServer
Function Add-DnsServerPrimaryZone DnsServer
Function Add-DnsServerResourceRecord DnsServer
Function Add-DnsServerResourceRecordA DnsServer
Function Add-DnsServerResourceRecordAAAA DnsServer
Function Add-DnsServerResourceRecordCName DnsServer
Function Add-DnsServerResourceRecordDnsKey DnsServer
Function Add-DnsServerResourceRecordDS DnsServer
Function Add-DnsServerResourceRecordMX DnsServer
Function Add-DnsServerResourceRecordPtr DnsServer
Function Add-DnsServerRootHint DnsServer
Function Add-DnsServerSecondaryZone DnsServer
Function Add-DnsServerSigningKey DnsServer
Function Add-DnsServerStubZone DnsServer
Function Add-DnsServerTrustAnchor DnsServer
Function Add-DnsServerZoneDelegation DnsServer
Function Clear-DnsServerCache DnsServer
Function Clear-DnsServerStatistics DnsServer
Function ConvertTo-DnsServerPrimaryZone DnsServer
Function ConvertTo-DnsServerSecondaryZone DnsServer
Function Disable-DnsServerSigningKeyRollover DnsServer
Function Enable-DnsServerSigningKeyRollover DnsServer
Function Export-DnsServerDnsSecPublicKey DnsServer
Function Export-DnsServerZone DnsServer
Function Get-DnsServer DnsServer
Function Get-DnsServerCache DnsServer
Function Get-DnsServerDiagnostics DnsServer
Function Get-DnsServerDirectoryPartition DnsServer
Function Get-DnsServerDnsSecZoneSetting DnsServer
Function Get-DnsServerDsSetting DnsServer
Function Get-DnsServerEDns DnsServer
Function Get-DnsServerForwarder DnsServer
Function Get-DnsServerGlobalNameZone DnsServer
Function Get-DnsServerGlobalQueryBlockList DnsServer
Function Get-DnsServerRecursion DnsServer
Function Get-DnsServerResourceRecord DnsServer
Function Get-DnsServerRootHint DnsServer
Function Get-DnsServerScavenging DnsServer
Function Get-DnsServerSetting DnsServer
Function Get-DnsServerSigningKey DnsServer
Function Get-DnsServerStatistics DnsServer
Function Get-DnsServerTrustAnchor DnsServer
Function Get-DnsServerTrustPoint DnsServer
Function Get-DnsServerZone DnsServer
Function Get-DnsServerZoneAging DnsServer
Function Get-DnsServerZoneDelegation DnsServer
powershell 传教士 原创文章 2015-11-25改允许转载，但必须保留名字和出处，否则追究法律责任
Function Import-DnsServerResourceRecordDS DnsServer
Function Import-DnsServerRootHint DnsServer
Function Import-DnsServerTrustAnchor DnsServer
Function Invoke-DnsServerSigningKeyRollover DnsServer
Function Invoke-DnsServerZoneSign DnsServer
Function Invoke-DnsServerZoneUnsign DnsServer
Function Register-DnsServerDirectoryPartition DnsServer
Function Remove-DnsServerDirectoryPartition DnsServer
Function Remove-DnsServerForwarder DnsServer
Function Remove-DnsServerResourceRecord DnsServer
Function Remove-DnsServerRootHint DnsServer
Function Remove-DnsServerSigningKey DnsServer
Function Remove-DnsServerTrustAnchor DnsServer
Function Remove-DnsServerZone DnsServer
Function Remove-DnsServerZoneDelegation DnsServer
Function Reset-DnsServerZoneKeyMasterRole DnsServer
Function Restore-DnsServerPrimaryZone DnsServer
Function Restore-DnsServerSecondaryZone DnsServer
Function Resume-DnsServerZone DnsServer
Function Set-DnsServer DnsServer
Function Set-DnsServerCache DnsServer
Function Set-DnsServerConditionalForwarderZone DnsServer
Function Set-DnsServerDiagnostics DnsServer
Function Set-DnsServerDnsSecZoneSetting DnsServer
Function Set-DnsServerDsSetting DnsServer
Function Set-DnsServerEDns DnsServer
Function Set-DnsServerForwarder DnsServer
Function Set-DnsServerGlobalNameZone DnsServer
Function Set-DnsServerGlobalQueryBlockList DnsServer
Function Set-DnsServerPrimaryZone DnsServer
Function Set-DnsServerRecursion DnsServer
Function Set-DnsServerResourceRecord DnsServer
Function Set-DnsServerResourceRecordAging DnsServer
Function Set-DnsServerRootHint DnsServer
Function Set-DnsServerScavenging DnsServer
Function Set-DnsServerSecondaryZone DnsServer
Function Set-DnsServerSetting DnsServer
Function Set-DnsServerSigningKey DnsServer
Function Set-DnsServerStubZone DnsServer
Function Set-DnsServerZoneAging DnsServer
Function Set-DnsServerZoneDelegation DnsServer
Function Show-DnsServerCache DnsServer
Function Show-DnsServerKeyStorageProvider DnsServer
Function Start-DnsServerScavenging DnsServer
Function Start-DnsServerZoneTransfer DnsServer
Function Step-DnsServerSigningKeyRollover DnsServer
Function Suspend-DnsServerZone DnsServer
Function Sync-DnsServerZone DnsServer
Function Test-DnsServer DnsServer
Function Test-DnsServerDnsSecZoneSetting DnsServer
Function Unregister-DnsServerDirectoryPartition DnsServer
Function Update-DnsServerTrustPoint DnsServer

手册在：
https://technet.microsoft.com/library/jj649850(v=wps.630).aspx

------------------------------具体问题-------------------------------------------------

guest问：dns轮询压力不均的问题
最近一直对DNS轮询有一个疑问，我在一个域名下挂了十多个IP，实现简单的负载均衡功能。
但明显发现这种轮询不是很均匀，有一台服务器上的压力始终很高，其他的比较接近，但将此台压力高的从dns列表上去掉后，列表中的下一台压力又会高起来，
此时再把先前那一台加回dns列表，压力就又转回到第一台上去了。已经排除了攻击的可能。这个问题一直困扰了我很久，不知是bind的bug还是我设置ttl或是其他什么的原因，不知有没有高人对这方面有所研究的？

user1答：
DNS 轮询机制会受到多方面的影响，如：A记录的TTL时间长短的影响；别的 DNS 服务器 Cache 的影响；windows 客户端也有一个 DNS Cache。
这些都会影响 DNS 轮询的效果。因此 DNS 的轮询机制并不能做为一个 load balancing 的解决方案，只能作为一个 load distribution 方案。

user2答：
绝大部分网民用的上网系统为WINDOWS系统，在WINDOWS系统上默认开启dns cache服务，这就造成在一个TTL周期内DNS轮询是无效的。
所以为了减小dns cache对轮询效果的影响，通行的做法是减小TTL的值如60-300。（TTL太小也是有害的，故而需要慎重对待）

# -------------------------问题分析过程------------------------------------

1 user1，user2说的很好，所以我引用了。ttl不宜太小正常即可。

2 我把这个过程比喻成，【呼啦超】那么多的食客来问你，你家的饭店的分店咋走，然后进你家的分店，去吃喝。
你家饭店假设有n家分店，每家最大接待能力不同，当前拥有的客人数不同，剩余的接待能力不同。

3 由于上两个人说的问题，导致了这样的结果，你不可能知道未来啥时候有食客来查分店地址（时间），因为有缓存，你也不可能知道未来食客具体有多少人（用户数量）。你也不可能知道查了ip的食客中，有多少会来吃。

4 所以说必须有一个【每分店剩余接待能力反馈】！
即有一个值，服务器连接失败，取值失败，则返回-1。-1太多则报警。
值为0，则代表分店客满，0太多则报警。我ps脚本就会跳过这家分店。
正常的值为0----1之间的数，表示这家分店的接の客能力。每个服务器有了这个数值供给，剩下就好办了。

5 为什么用win的dns？
答：
5.1 主要因为有powershell。
我记得bind无法用命令添加a记录等，并立即生效。这样的话拍马也赶不上powershell了！你要搞个程序去修改dns区域文件，然后reload分区。这样性能很差吧。
powershell命令管理dns是内存操作。主要用到的命令就这四个：
Add-DnsServerResourceRecordA，Add-DnsServerResourceRecordCName，Get-DnsServerResourceRecord，Remove-DnsServerResourceRecord

5.2 win的dns性能无问题：我记得bind是多进程的程序，可以用多核跑，无性能制约。而win2000中说win的dns比bind效率高。
dns还都是10年前都成熟的东西。退一万步来讲，win的dns玩一千个以下的ip解析，不会有性能问题。

5.3 可以用win做主dns服务器，linux+bind做缓存。win放内网，linux放外网，只从外网dns缓存取数据。

# -------------------------我给出的，问题解题流程------------------------------------

1 用任务计划，每1---n分钟定期调用powershell脚本解题。这里假设都是1分钟。
脚本运行后，把自己的pid放入环境变量，然后继续运行。
脚本重新运行后，从环境变量中查找前一个脚本的pid，杀死前一个脚本。然后重复上一步，避免脚本死锁。

2 用一个ps程序，从每家分店取回来，每家分店的（剩余）接待能力。
即有一个值，服务器连接失败，取值失败，则返回-1。-1太多则报警。
值为0，则代表分店客满，0太多则报警。我ps脚本就会跳过这家分店。
正常的值为0----1之间的数，表示这家分店的接の客能力。

powershell 传教士 原创文章 2015-11-25改允许转载，但必须保留名字和出处，否则追究法律责任
3 从这n家分店返回的接客能力中，总是挑2家最闲（数值最大）分店，把这2个新的a记录写入dns，然后删除所有旧的a记录。
设【取接待能力时间】为α秒，这里暗含着等待60-α-2秒。α如果较大则应增加任务计划分钟数。

结论：
我这个方法就是用dns来搞均衡，
我这个方法就是要告诉你，怎么搞的很均衡。
就是用我发明的填坑法，永远只填最大的两个坑！永远把最空闲的服务器的ip，放入dns服务器，解析给客户！

当然这里也可以用循环法，随机法，来进行负载均衡。

-------------------------解题具体脚本------------------------------------

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
#Requires -RunAsAdministrator
# 更换dns的a记录，到最大接待能力2台机的ip子上。

[string]$域名 = "aaaaxxxx.com"
[string]$主机名 = "ppp"
[int32]$服务器客满值 = 3
[int32]$服务器无响应值 = 2
$日志文件存储位置 = 'd:\aaa.txt'

$分店01 = @{'ip' = '1.2.3.4';'接待能力' = 0}
$分店02 = @{'ip' = '2.2.3.4';'接待能力' = 0}
$分店03 = @{'ip' = '3.2.3.4';'接待能力' = 0}
$分店04 = @{'ip' = '4.2.3.4';'接待能力' = 0}
$分店05 = @{'ip' = '5.2.3.4';'接待能力' = 0}
$分店06 = @{'ip' = '6.2.3.4';'接待能力' = 0}
$分店07 = @{'ip' = '7.2.3.4';'接待能力' = 0}
$分店08 = @{'ip' = '8.2.3.4';'接待能力' = 0}
$分店09 = @{'ip' = '9.2.3.4';'接待能力' = 0}
$分店10 = @{'ip' = '10.2.3.4';'接待能力' = 0}
$各分店集合 = $分店01,$分店02,$分店03,$分店04,$分店05,$分店06,$分店07,$分店08,$分店09,$分店10




if ($env:dnschanger_pid -ne $null)
{
    $temp001 = Get-Process -id $env:dnschanger_pid
    if ($temp001 -ne $null)
    {
        Stop-Process $temp001 -Force
    }
}
[System.Environment]::SetEnvironmentvariable("dnschanger_pid","$PID", "user")

function 获取分店接待能力
{
<#

用powershell监控win的CPU、IO、网络的处理能力，自古有之+网上大把。用powershell+ssh模块取linux的CPU、IO、网络、服务处理能力，还用我教你么？

这个函数返回一个值。
若服务器连接失败，或取值失败，则返回-1。-1太多则报警。
值为0，则代表分店客满，0太多则报警。我ps脚本就会跳过这家分店。
正常的返回值为0----1之间的数，表示这家分店的接の客能力。
#>







}

function 服务器客满报警
{


}


function 服务器无响应报警
{

}

# 无响应返回 -1，客满返回0，正常返回0----1间的值
$分店01.'接待能力' = 获取分店接待能力  "aaa" 123
$分店02.'接待能力' = 获取分店接待能力  "aaa" 123
$分店03.'接待能力' = 获取分店接待能力  "aaa" 123
$分店04.'接待能力' = 获取分店接待能力  "aaa" 123
$分店05.'接待能力' = 获取分店接待能力  "aaa" 123
$分店06.'接待能力' = 获取分店接待能力  "aaa" 123
$分店07.'接待能力' = 获取分店接待能力  "aaa" 123
$分店08.'接待能力' = 获取分店接待能力  "aaa" 123
$分店09.'接待能力' = 获取分店接待能力  "aaa" 123
$分店10.'接待能力' = 获取分店接待能力  "aaa" 123
$各分店接待能力集合 = $分店01.'接待能力',$分店02.'接待能力',$分店03.'接待能力',$分店04.'接待能力',$分店05.'接待能力',$分店06.'接待能力',$分店07.'接待能力',$分店08.'接待能力',$分店09.'接待能力',$分店10.'接待能力'
[system.array]::Sort($各分店接待能力集合)
$分组 = Group-Object -InputObject $各分店接待能力集合
if (($分组[0].name  -eq -1) -and ($分组[0].Count  -gt $服务器无响应值) )
{
    服务器无响应报警
    $err_msg = '服务器无响应!'
    Write-Error $err_msg
    $日期 = Get-date -Format F
    Add-Content    -Value $("$日期 $err_msg")  -LiteralPath  $日志文件存储位置
    exit 1
}

if (($分组[0].name  -eq 0) -and ($分组[0].Count  -gt $服务器客满值) )
{
    服务器客满报警
    $err_msg = '客满分店太多!'
    Write-Error $err_msg
    $日期 = Get-date -Format F
    Add-Content    -Value $("$日期 $err_msg")   -LiteralPath  $日志文件存储位置
    exit 2
}


# -----------------------------------------
$状元 = $各分店接待能力集合[-1]
$榜眼 = $各分店接待能力集合[-2]

foreach ($temp011 in $各分店集合)
{
    if ($temp011.'接待能力' -eq $状元)
    {
        [string]$ip1 = $temp011.'ip'
    }

    if ($temp011.'接待能力' -eq $榜眼)
    {
        [string]$ip2 = $temp011.'ip'
    }

}

#Import-Module  -name DnsServer
$旧的dns记录 = Get-DnsServerResourceRecord  -ZoneName $域名 -Name $主机名 -RRType "A"
$旧的ip = $旧的dns记录.RecordData.IPv4Address.IPAddressToString
Add-DnsServerResourceRecord -A  -ZoneName $域名  -Name $主机名  -IPv4Address $ip1 -TimeToLive 01:00:00
Add-DnsServerResourceRecord -A  -ZoneName $域名  -Name $主机名  -IPv4Address $ip2 -TimeToLive 01:00:00
#Add-DnsServerResourceRecord -CName -ZoneName $域名  -Name $主机名 -HostNameAlias "Host34.lab.com"  -TimeToLive 01:00:00
foreach ($temp002 in $旧的ip)
{
    Remove-DnsServerResourceRecord -ZoneName $域名  -Name $主机名  -RRType "A"  -RecordData  $temp002  -Force
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

http://www.cnblogs.com/piapia/p/4997916.html

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [dns负载均衡](https://www.cnblogs.com/piapia/tag/dns%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1/) , [负载均衡](https://www.cnblogs.com/piapia/tag/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1/)


