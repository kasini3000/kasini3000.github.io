据故老相传，运维界有句话：“脚林至尊，宝刀【ps1屠龙】，号令被控，莫敢不从”。

[https://gitee.com/chuanjiao10/kasini3000](https://gitee.com/chuanjiao10/kasini3000)

[https://gitee.com/chuanjiao10/kasini3000\_agent\_linux](https://gitee.com/chuanjiao10/kasini3000_agent_linux)

![](https://img2022.cnblogs.com/blog/456691/202202/456691-20220224184922181-90381304.jpg)

开源 + 免费 + 跨平台，欢迎给星，欢迎贡献代码，欢迎帮忙修复bug。

# ---【批量运维工具的点评】---

![](https://img2022.cnblogs.com/blog/456691/202202/456691-20220211155556294-1803800754.png)

问：已经有了ansible，你为什么还要开发kasini3000？你凭什么说kasini3000比ansible好10倍？

答：kasini3000中有完整的powershell。Ansible中有完整的python吗？

# 话题一：为啥不用ansible封装的rsync，而用卡死你3000封装的？

k\_rsync\_winfromlinux.ps1

k\_rsync\_wintolinux.ps1

Rsync是块同步，它只同步差异，对于大文件同步有优势。它要求主控机-被控机都用rsync来工作。Ansible和卡死你3000都调用底层的rsync，即你需要在主控机，被控机都装rsync。

对于linux-linux之间的rsync，卡死你3000是脚本源码，你可以很方便自己修改ps1脚本，调整里面的参数，达到rsync官方的参数，而ansible的rsync二手库，是一个二进制包，不方便更改参数。

对于win-linux之间的rsync，卡死你3000是封装了cwrsync客户端。而没有win主控机版ansible，win被控机也没有封装rsync，顾目前无此功能。

# 话题二：批量运维工具，必须得是脚本，这句话对吗？你怎么看？

很显然，ansible，salt违背了这一点。对于yaml，你可以随意定义任意语法。比如：

user: name={{ name }} password={{ chagepd | password\_hash('sha512') }} update\_password=always

上述为ansible改密码官方代码。

Ansible肆无忌惮地定义yaml。相关ansible库和yaml定义，就像一棵树，你明年再跑来看看，这树长得枝繁叶茂了。就像一个坑，你明年再跑来看看，坑更深、岔路更多了。若你入坑，你必然迷失坑中。要学习这些【第三方库=二手库】【每一个库的属性定义】，这无疑是一个大工程。你想改个密码而已，有必要学习那么多诸如passhash，always吗？ Kasini3000改密码：k\_run\_ip -ip 1.2.3.4 { chpasswd abcd1234 }

1 yaml有空格和缩进问题。 Powershell和kasini3000项目没有。还支持中文脚本名，中文函数名，中文参数名，参数值，中文报错信息，tab补全等。

2关键字不兼容。Ansible，阿里云的运维工具云助手，腾讯云的蓝鲸，都用yaml，但它们之间的关键字不兼容。

3脚本套脚本像喝水般容易，管道容易。Yaml套yaml难，yaml套脚本难。

4 For是shell脚本人最常用的功能。自打ansible来了之后，它用yaml关键字定义和jinjia2代替了for。可以说for的腿被被打折了。3层for循环难倒ansible老司机。

5 管道传值如“a脚本语言编写的脚本1 | b脚本语言编写的脚本1”，这在基于yaml的语言中，难以实现。

6 Python的对象，属性，方法，没有。处理对象费劲。表达if-else都费劲，套jinjia2不好调试。

7 如上所述。想把win，linux命令封装起来，只能是用各种脚本语言。不用脚本语言，却非要用yaml是很别扭的。

8运维必须得会win，linux原生命令。而Ansible致力于把win，linux命令封装成【参数、功能缩水的二手ansible库】。这在一定程度上没必要。

# 话题三：是脚本也未必行？

Ruby，perl，都是小众脚本，系统、运维、用的不多。

剩下的只有这么几种：

Shell：shell太老，有很多癌症。批量很难。

Python：py没有【shell级】命令行。管道使用麻烦。

Groovy：被限制在jenkins中，java占用内存大。但前景很好，是powershell的最大威胁。

Powershell：win占世界85%以上江山。

问：我自己写了脚本，为什么要用你的框架来运行？卡死你3000能干什么？

答：

它主要给脚本，提供多进程、多线程引擎；自动远程传递ps1脚本，和代码块；定时任务引擎；远程传值传对象；把结果入主控机sqlite数据库，方便查询。

# 话题四：有状态批量运维。

所谓状态，就是在批量运维动作前后，记录结果。状态可以被记录在主控机，被控机，各有好处。

Terraform，和powershell-dsc是有状态的典范。powershell-dsc可以在linux下运行，但模块不多。Powershell-dsc的优势是win。可以把卡死你3000，和powershell-dsc，安装在一起，即可在卡死你3000上，推送dsc任务和状态，来达到有状态运维。

Ansible，kasini3000本质是没有状态的。实际上，卡死你3000脚本内调用数据库，很容易手动实现有状态。或许未来，卡死你3000加litedb实现一个主控机单机的bson状态数据库，也可以添加mogodb的网络状态数据库的支持。

Shell和powershell脚本，要想实现有状态，很简单，用一个文本文件，记录状态结果即可。伪代码是这样：先cat log.txt |grep 软件a已经安装，是就退出，否就安装。删除也很简单，先cat log.txt |grep 软件a已经安装，是就删除软件，并删除这一行。

# 话题五：powershell和卡死你3000的超强扩展性。

l  Powershell支持所有主流数据库：sqlite，mogodb，litedb，redis，mysql，postgresql，sqlserver，oracle等。这些支持源于.net的nuget库，大都跨平台，部分支持arm。

l  卡死你3000主控机，和jenkins主控机，安装在一台机子上。支持从jenkins主控机中调用。

l  卡死你3000主控机，和vmware powercli主控机，安装在一台机子上。支持互操作。

l  卡死你3000主控机，和zabbix主控机，安装在一台机子上。相互调用传值。Zabbix被控机，可以调用卡死你3000被控机，也可以给卡死你kasini3000被控机传值。

l  卡死你3000的优势，是需要在被控机装powershell。若被控机是vmware esxi母机，或ssh交换机，也可以使用win，linux中的powershell的foreach加ssh实现批量运维。和多线程并发批量运维。

l  Aws云，azure云，都有官方powershell库。记住脚本就是胶水，调用完本地，就调用远程。

l  可以把【其他任意脚本】，【二进制库】，以字符串形式，嵌入ps1。

ansible-python之间如何对象传值？为了方便ansible-python之间对象传值，Ansble 做过什么？专门给和python开发传值功能么？如果没有，那ansible对python就是封闭的。

你可曾思考过扩展Ansible？

我曾经思考过【为何ansible把python语法、属性都给隐藏起来？】

但是要想暴露出来，却不容易。应该有【被控机有py2，有py3，统一这些很难】的问题。即便都是py3，不同版本也有差异。ansible开发团队，肯定会面临有这种问题的：ansible被控机需要跨越py2，py3，的鸿沟。
想象一下：
centos7被控机内只有py2，而ubuntu2004、或centos9，被控机内只有py3，你从同一台主控机，怎么分别控制它们？
同一个功能，肯定是要开发2套py脚本的。若想尽量避免2套脚本，就只能尽量使用最小，最通用的的py语句，特性。

你若用shell写扩展功能，那还不如用ps1写功能强，还能win，linux通用，还自带tab补全。

结论：想扩展ansible，找ansible官方，让开发者堆出库来，你只按规矩使用。Kasini3000这边自己写脚本。好处是powershell脚本不需要开发tab补全插件。所有自写脚本的参数，powershell引擎自动给你tab补全。

# 话题六：如何看待《卡死你3000》？

问：卡死你3000和ansible最大的区别是啥？

答：

1 卡死你3000是以远程执行脚本为核心的。

你需要写.ps1脚本，而不是yaml。ps1脚本和shell脚本95%相同。你可以在win，linux主控机上存储.sh;.py等传统shell脚本。并通过卡死你3000（的kct）向被控机推送脚本文件，并在被控机调用它，来实现100%兼容其他脚本。

你需要使用win，linux系统命令，而不是模块。

你需要用【echo '磁盘满了' ;exit 111】之类的shell语法，返回错误。需要写if-else去除不符的条件。

远程调用，100%兼容.py；.perl；.bat等脚本。支持被控机上不同版本的软件，同时存在。

2 卡死你3000有多线程，多进程。ansible只有多进程。

3 卡死你3000有远程传递对象。主控机到被控机传递对象参数，被控机到主控机返回对象结果，功能强大。

4 卡死你3000有【主控机级】远程定时任务。

问：卡死你3000的优势是啥？

l  卡死你3000win主控机，有声音，图片，视频报警，右下角气球报警，朗读中英文文本。在Win主控机使用，管理win被控机有极大优势。

l  卡死你3000主被控机，win，linux上安装简单，只需要从群共享中解压，并运行一个初始化脚本。Linux被控机上一键安装。Ansible不支持win主控机，linux主控机上安装麻烦，依赖包多。

l  【只需要1个】在主控机上的ps1脚本，即可穿透win，linux堡垒机，运行在被控机上。

l 中文脚本名，中文变量名，中文参数名，中文函数名。同一个参数名，支持中、英、日、韩、文，同时使用，随意输入。

a.ps1 -username 和a.ps1 -用户名，随便你用哪个，都是同一个参数。

l 你自己写的任意一个脚本，的参数，不需要编写tab补全，就支持tab补全。

l  Ansible这边是语法怪异，调试难。Powershell这边是win+vscode+powershell扩展+remote扩展+linux被控机，可以单步运行，断点，运行鼠标选中等调试。

l  Ansible这边是【返回值不可控制】。若改变值后再返回，主控机这边不认了。Powershell这边是你在被控机随意构造值，主控机任意处理。因为发送值，接受值，都受你控制。

卡死你3000是世界上最好的脚本批量框架。它比ansible，shell强。但它只是一个骨架，缺少毛皮肉。需要你写批量运维的业务脚本。

# 话题七：请用几句话，概括《ansible》和《卡死你3000》的总纲。

假如，把批量运维软件比喻成【武功秘籍】，那么可以把《卡死你3000》理解成藏在【中土神器】屠龙刀中的【武穆遗书】，其秘籍口诀总纲为【远程执行用krun，主控机到被控机复制kct（k-copy-to-被控机），被控机到主控机复制kcf（k-copy-from-被控机），多进程批量k-commit，多线程批量k-commit-rs】

若《ansible》是武功秘籍，那它必然是西域蕃僧所创的【批量大挪移】。其口诀总纲是啥？谁谈谈。不会是【应左则前，应有为后，三虚七实，无中生有】吧？

# ---【后记】---

1 你用过yaml批量运维工具吗？yaml套shell，和shell交互，传值，别扭吗？

2批量运维，为什么shell不行？在没有卡死你3000之前，为什么shell没打败ansible？成为批量运维主要工具。

3 搜烂ansible手册，也没找到ansible是多进程架构，多线程架构，还是都有。实际是多进程。这事为啥打死ansible也不说，谁能告诉我吗？

4 kasini3000缺点是啥？欢迎论点，论据。需要一键在linux上安装powershell，这算吗？不算吧。

5 红帽1000万美金收了ansible。Kasini3000值1100万美金吗？哪值？哪不值？给几个论点，论据。

有人愿意谈谈吗？

分类: [卡死你3000](https://www.cnblogs.com/piapia/category/1655558.html)

标签: [linux](https://www.cnblogs.com/piapia/tag/linux/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [shell](https://www.cnblogs.com/piapia/tag/shell/) , [ssh](https://www.cnblogs.com/piapia/tag/ssh/) , [win](https://www.cnblogs.com/piapia/tag/win/) , [winrm](https://www.cnblogs.com/piapia/tag/winrm/) , [脚本](https://www.cnblogs.com/piapia/tag/%E8%84%9A%E6%9C%AC/) , [批量](https://www.cnblogs.com/piapia/tag/%E6%89%B9%E9%87%8F/) , [批量运维](https://www.cnblogs.com/piapia/tag/%E6%89%B9%E9%87%8F%E8%BF%90%E7%BB%B4/)


