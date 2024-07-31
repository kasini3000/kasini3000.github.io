

# 此贴告诉你：为啥shell脚本人，不建议学python

py很强大，我承认。但在运维方面，py不但不强大，还有硬伤。正因为有下述硬伤，所以我们运维，还是用shell多，用py极少。
我看到用shell的人很多，你建议人用python，人说py是很好，但下一秒都跑光了。

```
  ------只从shell角度，详细对比powershell和python
```

# ------【1在linux安装powershell，并不是难题】------

绝大多数linux发行版，一键安装powershell：

[https://gitee.com/chuanjiao10/kasini3000\_agent\_linux](https://gitee.com/chuanjiao10/kasini3000_agent_linux)

centos7及以上，安装powershell:
curl -o /etc/yum.repos.d/microsoft.repo  https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum remove -y powershell #删除旧版
sudo yum install -y powershell
pwsh -c 'mkdir -p "\$env:HOME/.config/powershell" '
pwsh -c 'Add-Content  -Value "Set-PSReadlineOption -EditMode Windows" -LiteralPath \$profile '
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

ubuntu1604：
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo curl -o /etc/apt/sources.list.d/microsoft.list https://packages.microsoft.com/config/ubuntu/16.04/prod.list
sudo apt-get update
sudo apt-get remove -y powershell #删除旧版
sudo apt-get install -y powershell
pwsh -c 'mkdir  -p  "\$env:HOME/.config/powershell" '
pwsh -c 'Add-Content  -Value "Set-PSReadlineOption -EditMode Windows" -LiteralPath \$profile '
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

ubuntu1804：
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo curl -o /etc/apt/sources.list.d/microsoft.list https://packages.microsoft.com/config/ubuntu/18.04/prod.list
sudo apt-get update
sudo apt-get remove -y powershell #删除旧版
sudo apt-get install -y powershell
pwsh -c 'mkdir -p "\$env:HOME/.config/powershell" '
pwsh -c 'Add-Content  -Value "Set-PSReadlineOption -EditMode Windows" -LiteralPath \$profile '
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

debian9：
sudo apt-get update
sudo apt-get install curl gnupg apt-transport-https
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-debian-stretch-prod stretch main" > /etc/apt/sources.list.d/microsoft.list'
sudo apt-get update
sudo apt-get remove -y powershell #删除旧版
sudo apt-get install -y powershell
pwsh -c 'mkdir -p "\$env:HOME/.config/powershell" '
pwsh -c 'Add-Content  -Value "Set-PSReadlineOption -EditMode Windows" -LiteralPath \$profile '
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

debian10：
sudo apt-get update
sudo apt-get install curl gnupg apt-transport-https
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-debian-buster-prod buster main" > /etc/apt/sources.list.d/microsoft.list
sudo apt-get update
sudo apt-get remove -y powershell #删除旧版
sudo apt-get install -y powershell
pwsh -c 'mkdir -p "\$env:HOME/.config/powershell" '
pwsh -c 'Add-Content  -Value "Set-PSReadlineOption -EditMode Windows" -LiteralPath \$profile '
pwsh -c 'Add-Content  -Value "\`nSubsystem powershell /usr/bin/pwsh -sshs -NoLogo -NoProfile" -LiteralPath /etc/ssh/sshd\_config '

安装方法：
https://docs.microsoft.com/zh-cn/powershell/scripting/setup/installing-powershell-core-on-linux?view=powershell-6

# ------【2python定义变量，气死刘玄德】------

话说我在认识powershell之前，就想学习一门简单强大的脚本。我找到python开始学练起来。
有一天，我想定义个变量叫做【3顾茅庐】但不行，研究了下发现不支持数字打头。
改了下定义成了【a3顾茅庐】，感觉不雅，一怒之下改成了【b3顾茅庐】。。。
阿三顾茅庐，瘪三顾茅庐。。。姓刘的学个python真是倒霉啊， ；-（
第一步变量名都定义不了，还能平天下么？

同理，我想建立个变量叫做“52张扑克牌”python就不行。
\$52张扑克牌 = 'J','Q','K' #在powershell中合法
shell人喜欢用\$1,\$2,作为变量名，在py中也不行。ps无这些问题。ps函数名也支持数字打头，如1a。
\$1 = 'xxx'
\$2 = 'yyy'

在ps里，建议定义变量，引用变量，建议用大花括号。这一点和shell相同。
\${1} = 'xxx' #定义
\${1} #使用。

你在python中输入，if=1，会弹出，“duang，这是非法姨夫，姨夫是内置关键字，被python内部给霸占了”
你在powershell中，\$if=1，完全正常。if是姨夫，\$if是“四姨夫”，绝对不会搞错^\_^

下列为合法ps变量名的定义：
\${1+2} = 'a' #不加大花括号不行
\${a.b} = 'a'
\${a:b} = 'a'
\${a'b} = 'a'

我说py中，本质上，没有布尔型变量。ps中的布尔型变量强点有限。

那么什么叫做有布尔型变量呢？有且只有：1，0，"true","false",\$true,\$flase, 或许还要算上db的true，false。很显然py没做到，ps也没做到。

# ------【3python没有shell命令行】------

ps命令行，不光可以运行ps命令，还能执行shell外部命令。如find，grep等。
powershell有shell命令行，有参数，有tab补全，这是ps的一个大优势，py的一个大弱项。
或者说从.py中运行shell命令，接收返回值麻烦。
ps有命令行颜色，命令行表格&对齐（format-table），命令行百分比进度条（Write-Progress）等，这些命令行相关的基本功能，py都没有吧？

# ------【4python没有管道】------

powershell有管道，管道两端可以传强类型对象，和shell用法相同。这是ps的一个大优势，py的一个大弱项。
比如写py管道脚本，需要import，open，read，close。
py2有4个popen，
py3管道subprocess.Popen有各种坑，py为了解决这些坑，py有参数10多个，每个参数n个选项。导致在py中使用管道，变得很复杂。用起来很麻烦。
啥时候用true，啥时候用false参数？你得duang，duang实验。
这个麻烦直接把80%用户吓跑了。所以我“说成”没有管道。
而powershell，bash，使用管道数据，不需要这些步骤。

# ------【5shell命令，bash嵌入powershell容易，嵌入python难】------

基于上述3，4。shell，bash嵌入powershell容易，嵌入python难。
shell人学powershell难度平滑，shell人学python难度陡峭。
引号的绝对引用，powershell用【@''@】，【@""@】。python用3个单引号。哪个更容易嵌入shell呢？

# ------【6py语法vs ps语法】------

perl道：“来我告诉你，茴香豆的茴字，的4万种写法”
python道：“来我告诉你，茴香豆的茴字，的40种写法”
powershell道：“来我告诉你，茴香豆的茴字，的2种写法”
powershell语法简单，没有三目，没有装饰器，

比如\$aaa ,变量不管定义，还是引用，都要用\$ ，比python更容易看懂。比python的【无变量打头符号，定义变量】好。
powershell这种做法，更容易区分内置关键字，和变量！

python【变量名】支持大小写computer=1但Computer=2，很容易搞乱，让我用着不爽。

py的线程模型也不行。同1个python进程，能并发执行这2个py么？
bash=1进程。内含1个线程。此线程可以执行1个，单线程bash。
python=1进程。内含1个线程。此线程可以执行1个py。一个py内可以有多线程，【单py内的多线程代码】加了复杂度，却没法用cpu多核。
powershell=1进程。内含n个线程。每线程可以执行1个ps1。每线程内的ps1，和单线程执行ps1，差别极小。还能用多核。

由上述，py没有【ps的全局作用域】。我赶脚ps的变量作用域，比python清晰，简单。py混乱。

结论=论点：
python功能强大，但语法复杂，门槛高于ps。
powershell和shell语法类似，更适合脚本人员，运维人员，非专业程序员。
人生苦短，我用powershell

# ------【7py癌症1，中文&编码问题多】------

py的中文问题虽然都是可以解决的。但实际场合中，总是有中文问题。
常见的，处理中文文件名问题。url中带有中文问题。编码问题。

前两天有人给我个神奇软件，能把照片变果体。
我一看软件，用了python，gt5的壳子。
打开一个"美眉.jpg"，软件崩溃。改成英文名字，好了。

---a.py开始---

# 因为有本行中文注释。python2.7或python3.7 都不灵。除非加上【coding:】来声明编码，用【错误的编码来声明】也行。

print "Hello World!"
---a.py结束---

或

---b.py开始---

# 因为有本行中文注释。python2.7或python3.7 都不灵。除非加上【coding:】来声明编码，用【错误的编码来声明】也行。

print ("Hello World!")
---b.py结束---

powershell处理，lnux中的中文目录名，中文文件名，中文脚本名，中文变量名，中文函数名，中文注释，容易。

关于乱码：
乱码这个病，治法有很多，py大夫的治疗方案，我看不是最佳的。
微软家发明了bom头，powershell对bom头100%支持，
ps1脚本源码用bom头，txt用bom头。有头文件无乱码。

# ------【8py癌症2，无法锁死变量类型】------

强制动态类型的，py版胯骨轴子，太灵活了，太容易闪到腰。
py无法锁死变量类型，成为静态变量，真的很不爽。
这一点这很重要，甚至有人说py不适合大型项目。

锁死变量类型
https://www.v2ex.com/t/474197

而ps就不同了。
可以动态： \$a = 'xxx'
可以静态数据类型： [string]\$a = 'xxx'

# ------【9py癌症3，1.6亿美金估值的公司被py这个特性毁了】------

def f(l=[]):
l.append(1)
print(l)

f()
f()
f()
python2.7，python3.6.8测试结果为：
[1]
[1, 1]
[1, 1, 1]

围观：
https://www.v2ex.com/t/467817

powershell没有这个问题：
function f([System.Collections.ArrayList]\$l=@())
{
\$null = \$l.add(1)
\$l
}
f
f
f
输出：
1
1
1

# ------【10py癌症4，怎能少了你？python线程全局锁】------

python中的多线程，利用不了多核？这样岂不等于瞎子的眼睛。
搜  GIL  threading  Thread
powershell没这问题。

# ------【11py癌症5，路径依赖】------

你在当前目录下，存放一个【string.py】，【string.pyc】就把python打懵了。开始蹦吧报错。

同样道理，py脚本名还不能起名为ssl，http，request等。

python安装麻烦，升级麻烦。第三方库、包对路径有依赖，部署困难，移动困难，简直是癌症。
而ps较好，一个【\$env:PSModulePath】搞定。达到java的class-path水平。当然最好的是go的静态连接。

# ------【12py癌症6，py2和py3的兼容性】------

语法和模块差异较大，当然以下列py2老版本为主，新版本这个为题逐渐消解了。
centos6，centos7，centos8---centos8没有默认py3
ubuntu1404，ubuntu1804---ubuntu1604默认py3
debian8，debian9

# ------【13 py调试器】------

pycharm调试器需要破解，还是java开发的，又慢又吃内存。

Sublime Text 3主要问题：
1界面英文，汉化的不太敢用，怕后门。
2要破解。
3英文原版st3，打开带有【中文变量名】，【中文注释】的.py，.ps1会乱码。

py开发、调试器大都是英文的，来了vscode救赎。

# ------【14 手册】------

docs.microsoft.com中的。net手册，是多语种的，简单说就是中文的，比python的只有英文的手册强一万倍！！！
从py3.7开始，py也有中文手册了。这条不算了。

# ------【15 其他：】------

shell假如hang住，会给py带来麻烦。
python人太贵，运维的工资只能招到py低手。py高手有更挣钱的方向，【高富帅不愿入穷坑】写运维脚本。即便写出来。也很繁琐。又不好用。
py的远程ssh远程一堆坑  ： https://zhangge.net/5122.html
py的sftp一堆坑，不如ps+winscp模块和命令  ：    https://zhangge.net/5121.html
目的，生成个文件a，并写入内容b。遇到temp147的目录癌症了。不是这样的目录，就完全没问题。ps没这问题。shell也没这问题。

# ------【16 powershell的优势】------

ps中有，管道。管道两端可以传对象。
ps中有，基于sshd的，远程命令行。支持客户端，服务器之间，直接传输对象。py不行。或者说ps远程的【序列化/反序列化】是透明的。
ps的远程命令，可以自动上传脚本，到远程。
invoke-command -session \$a -filepath /tmp/客户机本地脚本1.ps1
ps有远程变量作用域，\$using:a = 1，本地引用远程变量，远程引用本地变量，容易。
ps用大花括号包围脚本代码，而不用单双引号。经堡垒机，多层服务器，发送命令，更简单，有优势。
用vscode，加remote-ssh插件，可以远程打开保存在linux中的ps1文件。
可以单步，断点，鼠标选中n行，可以在win中远程调试linux中的ps1脚本。
linux中可以多线程，多进程并发运行ps1。多线程并发可以跑满多核cpu。
powershellwin中无敌。
很多虚拟机厂家，云厂家，官方发布powershell模块：
aws，vmware，dell，联想，citrix
虽然powershell库没有python那么多，但运维方面的基本不缺。
powershell对json，yaml，csv支持的也很好。

# ------【17 爬虫】------

powershell+selenium爬虫，和python+selenium类似。95%以上的类库相同。只是语法有少许差别。

------【18 网站后端】------

python虽然有django，flask。但是太慢。小众。大家都用php,java,asp.net。powershell也能实现网站后端。

------【19 网站前端 】------

网站前端是js的天下，django多少不伦不类。

------【20 批量运维 】------

批量运维ansible和python其实无关。因为ansible无法和python进行对象级别的传值。用ansible比《卡死你3000》要费脑筋。
你若说ansible和python有关。那我问你，司机都要会设计车，会造车，会修车吗？

ansible基于yaml，语法别扭，需要学很多跟业务无关的参数。

kasini3000基于脚本。

# ------【20 机器学习 】------

你需要机器学习吗？python机器学习牛。机器学习需要研究算法很深，至少是it硕士。

------【21 数学，函数计算 】------

你需要免费的matlab吗？python函数计算牛。对标matlab。

# ------【31python在脚本范畴的优势在哪？】------

有啥python行，而powershell不行的，你倒是说出来呀？
欢迎来diss powershell。
注意，基本应该围绕，脚本，运维，shell这一块。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [python](https://www.cnblogs.com/piapia/tag/python/) , [缺点](https://www.cnblogs.com/piapia/tag/%E7%BC%BA%E7%82%B9/) , [shell](https://www.cnblogs.com/piapia/tag/shell/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)


