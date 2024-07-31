# 尽解powershell的workflow

# -------1【简介】---------

Microsoft .NET Framework 4.0 发布于2010年4月左右。
.net4 的新特性，是并行多任务机制。.net4 workflow（WF），翻译为工作流，它依赖并行多任务机制，并成为其延伸。（也有说法说WF在.net3中就有）

基于.net4的workflow，微软powershell团队开发出了powershell workflow。并作为powershell3.0的新特性推出，它是对.net4 workflow的封装，或调用。
powershell workflow中，并行语句产生的，每个并行任务，都会产生一个powershell。exe进程。是并发【多进程】!任务。
它弥补了powershell v2.0原先只有多线程（\*-job命令）的不足。

# --------2【浅谈多线程，多进程脚本的区别】---------

1 powershell job所有线程，都在一个powershell.exe进程中运行。powershell的多线程，可以使用多cpu核心，这点比python强。
bat，bash中没有多线程。powershell只有一个前台线程，无数个后台线程。

2 powershell workflow并行语句产生的，每个并行任务，都会产生一个powershell。exe进程。因为有进程创建时间所以比较慢，但并行提升了多任务速度。

3 进程的特色是隔离，从使用公用变量的角度，【多进程】不容易的，是不如【多线程】简单方便的。任何计算机语言都是这样。
但有了隔离，脚本代码跑的会更健壮。坏了就杀掉，不影响其他脚本。

4 从程序编写难易度，【多进程】比代码【多线程】更容易写，容易控制。任何计算机语言都是这样。

powershell 线程 进程 workflow Register-PSSessionConfiguration Set-PSSessionConfiguration
powershell 传教士 原创文章 允许转载，但必须保留名字和出处，否则追究法律责任。2014-03-29写，2019-09-18改，

5 不论是多进程，还是多线程，只能解决cpu密集计算的问题，只能解决io密集计算的问题。并发只是在用内存空间换时间，任何计算机语言都是这样。

# --------3 【workflow的原理和使用场景】---------

每个powershell.exe启动要2秒钟左右，占用内存80---100mb。所以你先要规划好内存。
假如说你有200个任务，并限定200个进程并发。
1workflow会先启动这200个powershell.exe，也就是400秒内，光吃cpu，内存了，没运行任务。

2把你的脚本任务，传给powershell运行。

3任务运行完毕后，这200个powershell.exe，坐等新任务，却等不到。

4超过一定时间后，这200个powershell.exe，开始被杀掉。

workflow使用场景：

1workflow首次启动非常慢。

2每个传入任务至少要10秒左右的，最好30秒以上。若传入任务用时太小，时间将被耗费在进程切换上。

3如果传入任务用时很小，还想用workflow，则应每次传入一组n个任务。

4每个powershell.exe，最少应该接活2次。即限定200个进程并发，则最少应该有400个任务。

# --------4 【workflow数据的输出】---------

1用一段代码，或一个脚本，来实现，并发写入txt。本质是多进程，同时写入txt。这里面必须有一个随机延时的功能。

分享powershell脚本：同一个txt文件，300个并发写入
http://bbs.chinaunix.net/thread-4314470-1-1.html

【bf并发写入txt\_v1.1.ps1】，脚本分享地址：
https://pan.baidu.com/s/16deKKe3ZnCg809lffiVZWg

2写入队列也可以。本质是多进程，通过端口，sql插件，写入另一个进程。在那个进程中，数据排队，再输出。

3写入数据库也可以。本质是用数据库的锁，实现并发到顺序。
比如，用一个脚本，写入数据库。等workflow执行完毕后，在任务脚本最后一行，调用另一个脚本，读取数据库，写入txt。

--------5 【powershell workflow最大进程数设置】---------

系统默认开5个多进程，
cpu密集型任务，应该根据cpu核心设定，比如有16核心，则开15，或16个进程。
io密集型任务，可以开100，200，这样。

#管理员权限，一次性执行此命令！

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
Enable-PSRemoting -SkipNetworkProfileCheck -Force
Set-NetFirewallRule -Name "WINRM-HTTP-In-TCP-NoScope" -RemoteAddress Any
Set-NetFirewallRule -Name "WINRM-HTTP-In-TCP" -RemoteAddress Any
Set-Item WSMan:\localhost\Client\TrustedHosts  -Value '*'
$我的工作流参数 = New-PSWorkflowExecutionOption   -MaxActivityProcesses 200
Register-PSSessionConfiguration -Name '我的工作流配置' -SessionTypeOption $我的工作流参数 -SessionType Workflow -Force
(Get-PSSessionConfiguration -Name '我的工作流配置' ).maxactivityprocesses
restart-service -Name WinRM -Force
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# --------6 【workflow脚本例子】---------

workflow最常用的功能，就是foreach -parallel

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$我的workflow代码 = {
 workflow  bingfa3
 {
  foreach -parallel ($renwu in 1..60)
  {
   inlinescript
    {
    Start-Sleep -Seconds 1
    $using:renwu
    Start-Sleep -Seconds 60
   }
  }
  #问：这个脚本谁写的？有问题找谁技术支持？
  #答：QQ群号=183173532
  #名称=powershell交流群
  # 2019-09-18 转载留名
 }
 bingfa3
}

$本机ip = '192.168.11.22'
$workflow连接1 = New-PSSession  -ComputerName $本机ip -ConfigurationName '我的工作流配置'
Invoke-Command -Session $workflow连接1  -ScriptBlock $我的workflow代码
Remove-PSSession $workflow连接1
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# --------7 【手册，资料】---------

powershell工作流            https://docs.microsoft.com/zh-cn/powershell/scripting/components/workflows-guide?view=powershell-5.1
New-PSWorkflowExecutionOption      https://docs.microsoft.com/zh-cn/powershell/module/psworkflow/new-psworkflowexecutionoption?view=powershell-5.1
About Workflows             https://docs.microsoft.com/zh-cn/powershell/module/psworkflow/about/about\_workflows?view=powershell-5.1
About WorkflowCommonParameters     https://docs.microsoft.com/zh-cn/powershell/module/psworkflow/about/about\_workflowcommonparameters?view=powershell-5.1

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [线程](https://www.cnblogs.com/piapia/tag/%E7%BA%BF%E7%A8%8B/) , [进程](https://www.cnblogs.com/piapia/tag/%E8%BF%9B%E7%A8%8B/) , [workflow](https://www.cnblogs.com/piapia/tag/workflow/) , [Register-PSSessionConfiguration](https://www.cnblogs.com/piapia/tag/Register-PSSessionConfiguration/) , [Set-PSSessionConfiguration](https://www.cnblogs.com/piapia/tag/Set-PSSessionConfiguration/)




