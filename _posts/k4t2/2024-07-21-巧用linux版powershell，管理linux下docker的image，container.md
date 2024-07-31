---
categories: k4t2
layout: post
permalink: /k4t2/巧用linux版powershell，管理linux下docker的image，container
---

powershell 传教士 原创文章 始于 2017-09-07 ,2018-07-16 ,2019-03-07 ,2020-04-21,2021-05-10 更新。允许转载，但必须保留名字和出处，否则追究法律责任

powershell,docker,cli,命令,docker ps,docker image

# 前 言

docker是这两年新兴的好东西，微软的powershell也来参与了。

docker ps是官方的管理命令，微软公司为这些命令制作了，雷同的山寨命令。

那么问题来了。powershell山寨出来的，马甲docker管理命令，有何好处？下详：

大家好，我把用powershell的docker马甲命令的好处，放在了页面下方，从第4章开始。

# 【第一章 在linux中搭建docker环境】

目的：
在任意版本的linux（物理机，虚拟机，节点机）中，安装docker服务。

动作：
教程=略，不是本文主要目的。请参考官网。

ubuntu
https://docs.docker.com/install/linux/docker-ce/ubuntu/

centos
https://docs.docker.com/install/linux/docker-ce/centos/

# 【第二章 安装linux版powershell】

目的：在linux发行版上，安装powershell。

动作：教程=略，不是本文主要目的。请参考官网。

https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-7

问：目前哪些版本操作系统上能安装上powershell？

答：

![](https://img2020.cnblogs.com/blog/456691/202005/456691-20200505152916510-176945734.jpg)

# 【第三章 安装powershell的，docker命令模块】

目的：给linux版powershell，安装docker模块。完成后，就有了马甲docker命令。

动作：

mkdir -p /usr/local/share/powershell/Modules

cd /tmp

git clone https://gitee.com/chuanjiao10/powershell\_docker\_module.git

cp -r  ./powershell\_docker\_module/\*  /usr/local/share/powershell/Modules/

查看有哪些马甲docker命令：
get-command -module docker

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
CommandType     Name                                               Version    Source
-------------------------------------------------------------------------------------
Alias           Attach-Container                                   0.1.0.111  Docker
Alias           Build-ContainerImage                               0.1.0.111  Docker
Alias           Commit-Container                                   0.1.0.111  Docker
Alias           Exec-Container                                     0.1.0.111  Docker
Alias           Load-ContainerImage                                0.1.0.111  Docker
Alias           Pull-ContainerImage                                0.1.0.111  Docker
Alias           Push-ContainerImage                                0.1.0.111  Docker
Alias           Run-ContainerImage                                 0.1.0.111  Docker
Alias           Save-ContainerImage                                0.1.0.111  Docker
Alias           Tag-ContainerImage                                 0.1.0.111  Docker
Cmdlet          Add-ContainerImageTag                              0.1.0.111  Docker
Cmdlet          ConvertTo-ContainerImage                           0.1.0.111  Docker
Cmdlet          Copy-ContainerFile                                 0.1.0.111  Docker
Cmdlet          Enter-ContainerSession                             0.1.0.111  Docker
Cmdlet          Export-ContainerImage                              0.1.0.111  Docker
Cmdlet          Get-Container                                      0.1.0.111  Docker
Cmdlet          Get-ContainerDetail                                0.1.0.111  Docker
Cmdlet          Get-ContainerImage                                 0.1.0.111  Docker
Cmdlet          Get-ContainerNet                                   0.1.0.111  Docker
Cmdlet          Get-ContainerNetDetail                             0.1.0.111  Docker
Cmdlet          Import-ContainerImage                              0.1.0.111  Docker
Cmdlet          Invoke-ContainerImage                              0.1.0.111  Docker
Cmdlet          New-Container                                      0.1.0.111  Docker
Cmdlet          New-ContainerImage                                 0.1.0.111  Docker
Cmdlet          New-ContainerNet                                   0.1.0.111  Docker
Cmdlet          Remove-Container                                   0.1.0.111  Docker
Cmdlet          Remove-ContainerImage                              0.1.0.111  Docker
Cmdlet          Remove-ContainerNet                                0.1.0.111  Docker
Cmdlet          Request-ContainerImage                             0.1.0.111  Docker
Cmdlet          Start-Container                                    0.1.0.111  Docker
Cmdlet          Start-ContainerProcess                             0.1.0.111  Docker
Cmdlet          Stop-Container                                     0.1.0.111  Docker
Cmdlet          Submit-ContainerImage                              0.1.0.111  Docker
Cmdlet          Wait-Container                                     0.1.0.111  Docker
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

其中：

pull-ContainerImage===Request-ContainerImage

Start-ContainerProcess===Exec-Container

Invoke-ContainerImage===Run-ContainerImage

Enter-ContainerSession===Attach-Container

Export-ContainerImage===Save-ContainerImage

简单使用：
Import-Module Docker
Get-Container  #这个命令就是 docker ps的马甲

【第四章 正式讲解powershell，在管理docker方面的好处】

目的1：
使用镜像名，或镜像名内包含的【字符串】来匹配容器，

动作1：
Get-Container | Where-Object {\$\_.Image -eq 'nginx'} |Stop-Container  #Get-Container相当于docker ps

讲解：
Get-Container，相当于docker ps。但是Get-Container返回的是对象。

【|】是管道。powershell的管道，是世界上唯一可以传输对象的。

Where-Object {} 是过滤语法。

Container的属性是：
\$\_.Image 是镜像名属性，其他属性是
\$\_.ID     字符串
\$\_.Command  字符串
\$\_.Created  日期型
\$\_.Status   字符串
\$\_.Names   字符串

image的属性是：
\$\_.ID          : sha256:的id
\$\_.ParentID    :
\$\_.RepoTags    : {k8s.gcr.io/kube-controller-manager:v1.13.2}
\$\_.RepoDigests :
\$\_.Created     : 19-1-11 上午12:06:28  日期型
\$\_.Size        : 146227986
\$\_.VirtualSize : 146227986
\$\_.Labels      :

Get-ContainerDetail 容器id 返回的属性是：

ID              : 386ce0ab3e798929ed8b5b27d52f53da86441ca4438a1d379dcc9cc11b73fabf

Created         : 19-3-11 上午7:51:56

Path            : /coredns

Args            : {-conf, /etc/coredns/Corefile}

State           : Docker.DotNet.Models.ContainerState

Image           : sha256:f59dcacceff45b5474d1385cd5f500d0c019ed9ca50ed5b814ac0c5fcec8699e

ResolvConfPath  : /var/lib/docker/containers/5c6a3b0b7a2fec7d1c6031e9db02490fae5263c44075185b3d7b2c843957cdef/resolv.conf

HostnamePath    : /var/lib/docker/containers/5c6a3b0b7a2fec7d1c6031e9db02490fae5263c44075185b3d7b2c843957cdef/hostname

HostsPath       : /var/lib/kubelet/pods/cc6bc767-180e-11e9-809f-00155d1ec606/etc-hosts

LogPath         : /var/lib/docker/containers/386ce0ab3e798929ed8b5b27d52f53da86441ca4438a1d379dcc9cc11b73fabf/386ce0ab3e798929ed8b5b27d52f53da864

```
              41ca4438a1d379dcc9cc11b73fabf-json.log
```

Node            :

Name            : /k8s\_coredns\_coredns-86c58d9df4-5ltfs\_kube-system\_cc6bc767-180e-11e9-809f-00155d1ec606\_5

RestartCount    : 0

Driver          : overlay2

MountLabel      :

ProcessLabel    :

AppArmorProfile :

ExecIDs         :

HostConfig      : Docker.DotNet.Models.HostConfig

GraphDriver     : Docker.DotNet.Models.GraphDriverData

SizeRw          : 0

SizeRootFs      : 0

Mounts          : {, , , }

Config          : Docker.DotNet.Models.Config

NetworkSettings : Docker.DotNet.Models.NetworkSettings

-eq是表达式。可以有如下表达式：
\$\_.Image -eq '字符串' #不区分大小写相等。 'abc' -eq 'Abc'
\$\_.Image -match '字符串' #正则匹配。 'abc' -match '^a.c\$'
\$\_.Image -like '\*字符串\*' # 'abc' -like '\*bc\*'
\$\_.数值型 -gt 123 # 456 -gt 123

\$a = [datetime]'2018-03-17'
\$\_.日期型 -gt \$a #

|Stop-Container，过滤完成后，输出一个或n个对象，然后用Stop-Container停止它。

---

目的2：
powershell命令行中，tab自动补全，（容器命令相关的）参数名，参数值

动作2-1：
Get-Container -c，然后打tab，

结果2-1：
命令行出现：
Get-Container -ContainerIdOrName

---

动作2-2：
Get-Container -ContainerIdOrName，然后打空格，然后依次打n次tab。

结果2-2：
命令行出现：
Get-Container -ContainerIdOrName 【docker ps 中列出的容器名】
依次打n次shift + tab则从后向前，列出容器名

---

动作2-3：
Get-ContainerImage  -，然后打tab。Get-ContainerImage相当于docker image ls

结果2-3：
命令行出现：
Get-ContainerImage -ImageIdOrName
再打空格，再打tab 出现【docker image ls 中列出的容器名】

---

场景：
同一个镜像，我运行了很多个，

ID                   Image           Command              Created                Status               Names

---

034c78b5e4e18b772... hello-world     /hello               18-3-19 下午5:20:03      Exited (0) 12 min... ecstatic\_khorana
8ced2c429e5ba0790... hello-world     /hello               18-3-17 下午9:05:00      Exited (0) 44 hou... objective\_villani
18cc9a6e1ea14ae8d... hello-world     /hello               18-3-9 下午5:03:08       Exited (0) 10 day... priceless\_ardingh...

目的3-1：
编写脚本，匹配镜像名，状态码

powershell脚本代码3-1：
\$状态码字串1 = Get-Container | Where-Object {\$\_.Image -eq 'hello-world'} | Select-Object Status
#\$状态码
#Exited (143) 24 hours ago
if (\$状态码字串1 -like '\*Exited (0)\*')
{echo  '正常'}
else
{这里做点什么}

---

目的3-2：
1匹配镜像名。
2排序，并输出
3取状态码

powershell脚本代码3-2：
\$状态码字串2 = Get-Container | Where-Object {\$\_.Image -eq 'hello-world'} |Sort-Object -Property Created |Select-Object Status -Last 1
\$状态码 = \$状态码字串2.status.split('(')[1].split(')')[0]

---

docker ps命令有输出对不齐的烦恼，实际上所有linux命令都有输出对不齐的情况，而powershell永远没有这种情况。

目的4：
只输出docker ps的，names   command

动作4：
Get-Container |Select-Object names,command
#或Get-Container |Select-Object names,image

输出：
Names                           Command

---

{/priceless\_ardinghelli}        /hello
{/zabbix}                       /config/bootstrap.sh
{/zabbix-db, /zabbix/zabbix.db} /run.sh
{/zabbix-db-storage}            sh
{/pedantic\_bose}                /hello

---

问：如何给docker ps的输出分行，分列？
答：

分行：
(Get-Container)[0]  #输出第一行

分列：
(Get-Container).Image  #输出image列

---

目的5： 我有3个网络，分别是，bridge，host，none，我想分别获取这些类别中,容器的端口，如何实现？

动作 5：

1 获取docker网络：这里以bridge为例

![](https://img2020.cnblogs.com/blog/456691/202105/456691-20210510230601214-1007686492.jpg)

2 获取 bridge网内含的【所有】容器id：这里只有1个

![](https://img2020.cnblogs.com/blog/456691/202105/456691-20210510230622067-1907979855.jpg)

3 获取这个容器id的，端口映射：

![](https://img2020.cnblogs.com/blog/456691/202105/456691-20210510230637425-480545882.jpg)

4把代码组合起来：

(Get-ContainerNet -id 868f).Containers.keys | ForEach-Object { (Get-ContainerDetail -ContainerIdOrName \$\_ ).NetworkSettings.Ports.keys   }

【第五章 结论】

powershell的马甲版docker命令，比docker原生命令，有如下优点：
1 输出的显示数据，永远没有对不齐的问题。
2 powershell马甲版docker命令，是面向对象的。通过属性匹配docker的名字和参数，表达式支持正则，更强大。比用grep简单，严谨。
3 powershell的管道，支持对象。用起来强大简单。
4 powershell马甲版docker命令的，参数名，参数值，支持tab自动补全，用起来十分方便。
5 ps的马甲命令Get-Container，相当于docker ps。
它的输出，分行，分列。如下：用起来比docker ps输出的字符串要好。

ID  Image  Command  Created  Status  Names

---

需要说明的是，这些优点，本质上和docker命令无关。这是powershell命令行，比linux命令行的优点。

从前，有一头“青翼蝠王”韦瞎笑，在z呼发贴说：“管道中传对象，是没必要的”。是这样么？

围观下面这人，狂扣字符串，很好地说明了这一点。

此人问：请问下如何得到

![](https://img2020.cnblogs.com/blog/456691/202005/456691-20200505153812331-1330513171.jpg)

port这一列字符串，我开始用的方法是awk '{print \$11}') 可是发现status下面的时间长度是不固定的，可能三列可能四列

我的这篇帖子，很好地说明了这一点。

-----------------------------谢谢看贴，完。-------------------------------

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [docker](https://www.cnblogs.com/piapia/tag/docker/) , [docker ps](https://www.cnblogs.com/piapia/tag/docker%20ps/) , [docker image](https://www.cnblogs.com/piapia/tag/docker%20image/) , [docker container](https://www.cnblogs.com/piapia/tag/docker%20container/) , [容器](https://www.cnblogs.com/piapia/tag/%E5%AE%B9%E5%99%A8/) , [镜像](https://www.cnblogs.com/piapia/tag/%E9%95%9C%E5%83%8F/)
