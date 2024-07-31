


# powershell玩转iis网站服务器

# 1 ------------安装------------------

for win7,win8,win8.1,win10
控制面板---》程序和功能---》开启关闭windows功能---》iis---》web管理工具---》iis管理脚本和工具---》打勾选中点确定。

# 2 ----------win2019 命令列表----------------

PS C:\\Users\\Administrator> get-command -module IISAdministration

CommandType     Name                                               Version    Source

---

Cmdlet          Clear-IISCentralCertProvider                       1.1.0.0    IISAdministration
Cmdlet          Clear-IISConfigCollection                          1.1.0.0    IISAdministration
Cmdlet          Disable-IISCentralCertProvider                     1.1.0.0    IISAdministration
Cmdlet          Disable-IISSharedConfig                            1.1.0.0    IISAdministration
Cmdlet          Enable-IISCentralCertProvider                      1.1.0.0    IISAdministration
Cmdlet          Enable-IISSharedConfig                             1.1.0.0    IISAdministration
Cmdlet          Export-IISConfiguration                            1.1.0.0    IISAdministration
Cmdlet          Get-IISAppPool                                     1.1.0.0    IISAdministration
Cmdlet          Get-IISCentralCertProvider                         1.1.0.0    IISAdministration
Cmdlet          Get-IISConfigAttributeValue                        1.1.0.0    IISAdministration
Cmdlet          Get-IISConfigCollection                            1.1.0.0    IISAdministration
Cmdlet          Get-IISConfigCollectionElement                     1.1.0.0    IISAdministration
Cmdlet          Get-IISConfigElement                               1.1.0.0    IISAdministration
Cmdlet          Get-IISConfigSection                               1.1.0.0    IISAdministration
Cmdlet          Get-IISServerManager                               1.1.0.0    IISAdministration
Cmdlet          Get-IISSharedConfig                                1.1.0.0    IISAdministration
Cmdlet          Get-IISSite                                        1.1.0.0    IISAdministration
Cmdlet          Get-IISSiteBinding                                 1.1.0.0    IISAdministration
Cmdlet          New-IISConfigCollectionElement                     1.1.0.0    IISAdministration
Cmdlet          New-IISSite                                        1.1.0.0    IISAdministration
Cmdlet          New-IISSiteBinding                                 1.1.0.0    IISAdministration
Cmdlet          Remove-IISConfigAttribute                          1.1.0.0    IISAdministration
Cmdlet          Remove-IISConfigCollectionElement                  1.1.0.0    IISAdministration
Cmdlet          Remove-IISConfigElement                            1.1.0.0    IISAdministration
Cmdlet          Remove-IISSite                                     1.1.0.0    IISAdministration
Cmdlet          Remove-IISSiteBinding                              1.1.0.0    IISAdministration
Cmdlet          Reset-IISServerManager                             1.1.0.0    IISAdministration
Cmdlet          Set-IISCentralCertProvider                         1.1.0.0    IISAdministration
Cmdlet          Set-IISCentralCertProviderCredential               1.1.0.0    IISAdministration
Cmdlet          Set-IISConfigAttributeValue                        1.1.0.0    IISAdministration
Cmdlet          Start-IISCommitDelay                               1.1.0.0    IISAdministration
Cmdlet          Start-IISSite                                      1.1.0.0    IISAdministration
Cmdlet          Stop-IISCommitDelay                                1.1.0.0    IISAdministration
Cmdlet          Stop-IISSite                                       1.1.0.0    IISAdministration

PS C:\\Users\\Administrator> Get-Command -module WebAdministration

CommandType     Name                                               Version    Source

---

Function        IIS:                                               1.0.0.0    WebAdministration
Cmdlet          Add-WebConfiguration                               1.0.0.0    WebAdministration
Cmdlet          Add-WebConfigurationLock                           1.0.0.0    WebAdministration
Cmdlet          Add-WebConfigurationProperty                       1.0.0.0    WebAdministration
Cmdlet          Backup-WebConfiguration                            1.0.0.0    WebAdministration
Cmdlet          Clear-WebCentralCertProvider                       1.0.0.0    WebAdministration
Cmdlet          Clear-WebConfiguration                             1.0.0.0    WebAdministration
Cmdlet          Clear-WebRequestTracingSetting                     1.0.0.0    WebAdministration
Cmdlet          Clear-WebRequestTracingSettings                    1.0.0.0    WebAdministration
Cmdlet          ConvertTo-WebApplication                           1.0.0.0    WebAdministration
Cmdlet          Disable-WebCentralCertProvider                     1.0.0.0    WebAdministration
Cmdlet          Disable-WebGlobalModule                            1.0.0.0    WebAdministration
Cmdlet          Disable-WebRequestTracing                          1.0.0.0    WebAdministration
Cmdlet          Enable-WebCentralCertProvider                      1.0.0.0    WebAdministration
Cmdlet          Enable-WebGlobalModule                             1.0.0.0    WebAdministration
Cmdlet          Enable-WebRequestTracing                           1.0.0.0    WebAdministration
Cmdlet          Get-WebAppDomain                                   1.0.0.0    WebAdministration
Cmdlet          Get-WebApplication                                 1.0.0.0    WebAdministration
Cmdlet          Get-WebAppPoolState                                1.0.0.0    WebAdministration
Cmdlet          Get-WebBinding                                     1.0.0.0    WebAdministration
Cmdlet          Get-WebCentralCertProvider                         1.0.0.0    WebAdministration
Cmdlet          Get-WebConfigFile                                  1.0.0.0    WebAdministration
Cmdlet          Get-WebConfiguration                               1.0.0.0    WebAdministration
Cmdlet          Get-WebConfigurationBackup                         1.0.0.0    WebAdministration
Cmdlet          Get-WebConfigurationLocation                       1.0.0.0    WebAdministration
Cmdlet          Get-WebConfigurationLock                           1.0.0.0    WebAdministration
Cmdlet          Get-WebConfigurationProperty                       1.0.0.0    WebAdministration
Cmdlet          Get-WebFilePath                                    1.0.0.0    WebAdministration
Cmdlet          Get-WebGlobalModule                                1.0.0.0    WebAdministration
Cmdlet          Get-WebHandler                                     1.0.0.0    WebAdministration
Cmdlet          Get-WebItemState                                   1.0.0.0    WebAdministration
Cmdlet          Get-WebManagedModule                               1.0.0.0    WebAdministration
Cmdlet          Get-WebRequest                                     1.0.0.0    WebAdministration
Cmdlet          Get-Website                                        1.0.0.0    WebAdministration
Cmdlet          Get-WebsiteState                                   1.0.0.0    WebAdministration
Cmdlet          Get-WebURL                                         1.0.0.0    WebAdministration
Cmdlet          Get-WebVirtualDirectory                            1.0.0.0    WebAdministration
Cmdlet          New-WebApplication                                 1.0.0.0    WebAdministration
Cmdlet          New-WebAppPool                                     1.0.0.0    WebAdministration
Cmdlet          New-WebBinding                                     1.0.0.0    WebAdministration
Cmdlet          New-WebFtpSite                                     1.0.0.0    WebAdministration
Cmdlet          New-WebGlobalModule                                1.0.0.0    WebAdministration
Cmdlet          New-WebHandler                                     1.0.0.0    WebAdministration
Cmdlet          New-WebManagedModule                               1.0.0.0    WebAdministration
Cmdlet          New-Website                                        1.0.0.0    WebAdministration
Cmdlet          New-WebVirtualDirectory                            1.0.0.0    WebAdministration
Cmdlet          Remove-WebApplication                              1.0.0.0    WebAdministration
Cmdlet          Remove-WebAppPool                                  1.0.0.0    WebAdministration
Cmdlet          Remove-WebBinding                                  1.0.0.0    WebAdministration
Cmdlet          Remove-WebConfigurationBackup                      1.0.0.0    WebAdministration
Cmdlet          Remove-WebConfigurationLocation                    1.0.0.0    WebAdministration
Cmdlet          Remove-WebConfigurationLock                        1.0.0.0    WebAdministration
Cmdlet          Remove-WebConfigurationProperty                    1.0.0.0    WebAdministration
Cmdlet          Remove-WebGlobalModule                             1.0.0.0    WebAdministration
Cmdlet          Remove-WebHandler                                  1.0.0.0    WebAdministration
Cmdlet          Remove-WebManagedModule                            1.0.0.0    WebAdministration
Cmdlet          Remove-Website                                     1.0.0.0    WebAdministration
Cmdlet          Remove-WebVirtualDirectory                         1.0.0.0    WebAdministration
Cmdlet          Rename-WebConfigurationLocation                    1.0.0.0    WebAdministration
Cmdlet          Restart-WebAppPool                                 1.0.0.0    WebAdministration
Cmdlet          Restart-WebItem                                    1.0.0.0    WebAdministration
Cmdlet          Restore-WebConfiguration                           1.0.0.0    WebAdministration
Cmdlet          Select-WebConfiguration                            1.0.0.0    WebAdministration
Cmdlet          Set-WebBinding                                     1.0.0.0    WebAdministration
Cmdlet          Set-WebCentralCertProvider                         1.0.0.0    WebAdministration
Cmdlet          Set-WebCentralCertProviderCredential               1.0.0.0    WebAdministration
Cmdlet          Set-WebConfiguration                               1.0.0.0    WebAdministration
Cmdlet          Set-WebConfigurationProperty                       1.0.0.0    WebAdministration
Cmdlet          Set-WebGlobalModule                                1.0.0.0    WebAdministration
Cmdlet          Set-WebHandler                                     1.0.0.0    WebAdministration
Cmdlet          Set-WebManagedModule                               1.0.0.0    WebAdministration
Cmdlet          Start-WebAppPool                                   1.0.0.0    WebAdministration
Cmdlet          Start-WebCommitDelay                               1.0.0.0    WebAdministration
Cmdlet          Start-WebItem                                      1.0.0.0    WebAdministration
Cmdlet          Start-Website                                      1.0.0.0    WebAdministration
Cmdlet          Stop-WebAppPool                                    1.0.0.0    WebAdministration
Cmdlet          Stop-WebCommitDelay                                1.0.0.0    WebAdministration
Cmdlet          Stop-WebItem                                       1.0.0.0    WebAdministration
Cmdlet          Stop-Website                                       1.0.0.0    WebAdministration

# 3--------官方文档--------

win7
https://docs.microsoft.com/zh-cn/previous-versions/windows/powershell-scripting/hh867899(v=wps.630)

win8，win2012
https://docs.microsoft.com/zh-cn/powershell/module/webadministration/add-webconfiguration?view=winserver2012r2-ps

win10，win2016
https://docs.microsoft.com/zh-cn/powershell/module/iisadministration/clear-iiscentralcertprovider?view=win10-ps

# 4 --------常见问题--------

问：
IIS 网站 并发连接线不多，但是运行一段时间后 就非常慢，
系统资源占用都正常，一回收应用程序池就又好了。
或者有时候假死，咨询下,如何解决呢？

答：
需要开发调试网站程序，除去bug。
对于运维来说，只能先用计划任务，定时重启程序池。相关的powershell命令是：
Stop-WebAppPool   ，Start-WebAppPool

对于iis建议使用win10，或win2016服务器版。只有它才支持http 2.0，传输数据更快。
用win10虚拟机测试1---2天，如果假死问题消失了，那就是低版本iis有bug。

url-rewrite
https://www.iis.net/downloads/microsoft/url-rewrite

从win2012开始，基本淘汰了appcmd，喜欢古董命令的话，当然也可以用。
现在都用powershell模块WebAdministration中的命令管理iis。

而win2016，2019开始，拆分为两个模块：
1WebAdministration
2IISAdministration
ps传教士 编写 2019-01-17
而win2012r2只有WebAdministration模块

# 5 --------例子--------

一般来说建立iis站点和配置，不太可能批量。所以说很多命令用不到。建议用图形的【iis站点管理器】即可。

而更新网站，一般的步骤为：常用的就这么几个命令
1 IISAdministration/Stop-IISSite 网站名
2 WebAdministration/Stop-WebAppPool 池名

3 copy-item 源文件或源目录 iis:\\sites\\网站名\\

4 WebAdministration/Start-WebAppPool 池名
5 IISAdministration/Start-IISSite 网站名

你们的iis网站，都是停服后更新？还是开服热更新啊？一般静态网页，可以开服更新。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [iis](https://www.cnblogs.com/piapia/tag/iis/) , [IISAdministration](https://www.cnblogs.com/piapia/tag/IISAdministration/) , [WebAdministration](https://www.cnblogs.com/piapia/tag/WebAdministration/)



