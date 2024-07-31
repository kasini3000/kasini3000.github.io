2022-10-05 nushell版本=v0.69.1

Nushell powershell awk 对比 第二集 博客园

# 第一章 对比awk，nushell，powershell的安装

1 nushell是mit协议的，rust开发的，【单文件程序！！！】，所以nushell的安装真香！

2 从这点上来看awk完败。Awk文件太多不绿色。Awk就占个内置的先机。

3 powershell是绿色软件，从github下载后，解压便可执行，但它是单目录中，有多个文件和子目录。这比awk安装在多个不同的子目录要好。我还为大家做了linux一键安装powershell的脚本。

安装脚本在码云，Linux下一键安装powershell：

https://gitee.com/chuanjiao10/kasini3000\_agent\_linux

结论：nushell和powershell都是绿色linux软件。

# 第二章 对比nushell，powershell的功能

1 nushell

关于nushell的扩展性，目前我还是不看好。和powershell对比还是不行。

不建议powershell人学习nushell，因为学了之后，你会从西施变成东施。Nushell中没有对象，也就没有属性，方法。但有模仿的似是而非的东西，而且模仿的痕迹还特别大。比如：

Powershell：

‘abc’.toupper()

Nutshell:

‘abc’ | str upcase

Ls | where size > 5mb | sort-by name

关于nushell的适用场景：

awk最大的竞争对手，敌人，就是nushell。它可以代替awk，sed，可以支持表，json，xml，bson，sqlite等，支持多线程并发，支持特别老的linux，和unix。它们的战场（对比场景），就是常见的【linux本地不规范数据处理】。

世界上90%的人，都用linux的默认shell，如bash，ash等。只有不到5%的linux人把默认shell替换成了powershell。用nushell当默认shell的人就更少了。也就是说，几乎没人拿nushell当shell用，而拿nushell当awk用。用nushell来处理数据，扣取字串，分行分列，发送接收json等。

2 powershell：Win，linux中，最强大的还属powershell。

Powershell的强大，主要通过4点：

1. net。powershell基于.net，powershell目录文件中，90%以上是.net文件。Powershell用的是.net对象，属性，方法。
2. 通过从nuget下载的.net的dll文件，给powershell各种扩展。这些扩展是跨平台即win，linux通用的。如：在win、linux中支持各种数据库。
3. 强大联盟。微软在win10，win11，winserver，linux，azure。亚马逊在aws云，vmware在powercli中使用powershell。
4. 我开发的kasini3000，这是一个开源，免费，跨平台的，脚本级别的批量运维工具。而ansible是yaml级别的。可以通过【kasini3000】同时控制多个云。同时控制公有云，私有云。同时控制win，linux被控机。

Powershell和Kasini3000中有个功能，就是基于powershell的new-psssession的双向远程传对象。请看：

```powershell
$时间对象_从被控机传到主控机 = k_run_ip -ip 被控机ip地址 {get-date}
$时间对象_从被控机传到主控机.addday(3) #被控机时间 + 3天
```

powershell管道传对象被你nushell无情地山z寨了。我想看看powershell的远程传对象，你怎么山z寨。

# 第三章 接续第一集，继续探究nushell的功能。

关于官方的，把nushell看成编译脚本：

这一个特性真的不好。Powershell没有这个特性。

nushell第一集中的问题:可以定义带有点的变量，却无法使用，什么鬼？如：let a.a = 1 ,用\$a.a  \${a.a} 不能输出。------已经修复，现在不能定义上述变量。

nushell第一集中的问题:在win的nushell中，的文件名补全。可以补全目录名（支持），目录中的文件名，有时候不能自动补全。文件名补全，打一个字母后，不能补全，这个应该是个bug。------已经修复

nutshell中，这一个特性很好：

let if = 1 ，let 1 = ‘a’ ，都可以定义。并使用\$if,\$1。这和powershell相同。Python中不允许这么用，而脚本人喜欢用 \$1  ，  \$2。

nushell第2集发现的新问题:

管道可以和powershell联用，但有问题：

pwsh.exe -c "echo a" | str upcase

A

----上述好多空格

echo 'a' | str upcase

A  ----正常

ls | autoview  ---不灵了，应该是autoview命令删除了

\$it ---不灵了，没了。

这个命令可以：

'aaa 分割符 bbb' | parse '{a1} 分割符 {a2}' | get a1

这个命令不行：让我感到难受

'aaa 分割符 bbb' | parse '{1} 分割符 {2}' | get 1    #不行

'aaa 分割符 bbb' | parse '{\$1} 分割符 {\$2}' | get \$1  #不行

结论：

Python败在没有shell级别的命令行，py脚本内支持管道很麻烦。不建议使用。

而powershell是最简单，功能最强的。它语法和shell 95%相同。库随不如py强，但对脚本，运维人员来讲人足够了。

awk是一个传统的【字符串级别】shell语言工具。Awk是shell的架海紫金梁。既然是【字符串级别】工具，那就免不了依赖正则。

nushell是一个【半面向对象级别】的shell工具。是通天灵宝的仿a制b品（寨c版）。Nushell通过伪对象，伪属性，伪方法来实现功能。nushell有管道，而python可以看做没有管道。它支持sql库=sqlite，nosql库=bson，用起来比py简单，它支持linux外部命令。Nushell是单文件绿色软件，这点让它真香！nushell目前最大的对手是awk。但nushell扩展性不如powershell，python。

Shell工具本来是小众软件。前有【饭醉团dd伙3人组awk-sed-grep】，后有强大的【powershell抽喝烫】。nushell浑身都是假%z货（伪对象，伪属性，伪方法），nushell能杀出重围吗？敬请期待第3集。

欢迎awk支持者，参与讨论。意见不同也很正常。

Shell用户受困于awk，这导致excel文件=库，mysql库，sqlite库，bson=nosql库，不能方便地被shell用户使用。Awk和txt又不方便多核cpu。而nushell的出现，部分解决了这种问题。


