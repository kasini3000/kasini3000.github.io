---
categories: k4t2
layout: post
permalink: /k4t2/k4t系统架构 & 部署最佳实践
---




# 《kaiiit船长》 容器自动管理软件 （k4t，国产k8s）

# k4t系统架构 & 部署最佳实践

文档修订时间： 2021-11-20

关键词 : k4t kaiiit 船长 captain powershell 最佳实践 容器 docker ps1倚天剑 国产k8s ps1绣花针

---

# k4t系统架构

![k4t图](https://img2020.cnblogs.com/blog/456691/202111/456691-20211117163958061-473798672.png)

# 问：完美版的k4t项目，由哪几部分组件构成？

# 答：

### 1 master端。

### 2 node端。

### 3 动态负载均衡api网关。（外部 非必要组件）

### 4 动态dns。（外部）

### 5 监控端。

---

# k4t部署相关问题之【master端】

### 概述：

k4t master相当于经理的手。干的是分配任务的活。采用懒惰的【触发执行】机制。

下列情况下，k4t master可以关机，不影响node运行。

* 任务分配完毕，或调整完毕。
* 没有启用，通过【k4t监控端】，进行【node自动伸缩】功能。
* 没有执行，驱逐node任务（drain）。
* 没有执行，删除node任务。

### 问：k4t master需要几台？

答：最多1台。或执行任务完毕后就关机的，半台master。

### 问：1台k4t master的话，如何保证元数据冗余？

答：依靠k4t node。k4t master软件内有，复制元数据到node的功能。只需要指定一个node ip地址即可。

可以灵活设定：可以设置1---n台node，每node可以设置1---n分钟，不同的备份时间。这样就可以有多个不同时段的，元数据历史副本。

实验环境-最简实践：不指定node，不备份元数据。

生产级别-最佳实践：设定1台node，设置每1---2分钟备份元数据。

---

# k4t部署相关问题之【监控端】

### 概述：

【k4t 监控端】相当于经理（k4t master）的眼睛。

【k4t 监控端】由若干powershell脚本组成。

任务是检查k4t master内，k4t node内，磁盘空间，内存剩余，cpu剩余，和node是否存活等。

### 问：【k4t 监控端】需要几台？

答：1台。

【k4t 监控端】可以和【k4t master】分别安装在，2台虚拟机内。
建议把【k4t 监控端】和【k4t master】分离。把【k4t 监控端】装在你公司旧有的win，linux的zabbix监控机中。

【k4t 监控端】可以和【k4t master】安装在同一台虚拟机中。这样【k4t master】就丢失了【干完活关机】的功能。

实验环境-最简实践：【k4t 监控端】可以和【k4t master】安装在一台虚拟机中。
生产级别-最佳实践：【k4t 监控端】可以和【k4t master】安装在一台虚拟机中。。或分离。

---

# k4t部署相关问题之【k4t网关】

### 概述：

【k4t网关】给k4t引入容器间的，东西向流量。负载平衡，限流等。
【k4t网关】可以支持任意第三方网关。本质上，2台虚拟机内的网关，可以不同厂家，不同版本。

### 问：【k4t网关】需要几台？

答：0---2台。

### 问：什么情况下，需要0台【k4t网关】？

答：下列极端情况下。

* 整个集群内，都采用ipv6地址。
* 整个集群内，都采外网ipv4地址。如 202.x.x.x
* 使用云dnat网关，提供引入流量服务。

实验环境-最简实践：1台虚拟机中，安装【k4t 网关】，和【k4t dns】。
生产级别-最佳实践：2台虚拟机中，安装【k4t 网关】，和【k4t dns】。用于冗余高可用。采用相同厂家的网关软件，软件版本相同。

---

# k4t部署相关问题之【k4t-dns】

### 概述：

【k4t-dns】帮k4t实现【服务发现】功能。
【k4t-dns】可以支持任意第三方dns。支持win server的dns server。
本质上，2台虚拟机内的dns，支持不同厂家，不同版本。

### 问：【k4t-dns】需要几台？

答：1---2台。至少有1台。

实验环境-最简实践：1台虚拟机中，安装【k4t 网关】，和【k4t dns】。
生产级别-最佳实践：2台虚拟机中，安装【k4t 网关】，和【k4t dns】。用于冗余高可用。采用相同厂家的网关软件，软件版本相同。

---

# k4t部署相关问题之【双网关】，或【双dns】，之间数据同步问题。

### 概述：

采用【客户端分布式】技术。类似于etcd。原理就是分布式中的，基于redo-log，2步提交。

这样做的好处是，支持不同厂家，不同版本。不需要第三方网关软件带有双机高可用功能。不需要第三方dns软件带有双机高可用功能。

---

# k4t部署相关问题之【结论】

### 实验环境-最简实践：总共2台虚拟机。

* 1台虚拟机中，安装【k4t master】，和【k4t 监控端】。
* 1台虚拟机中，安装【k4t网关】，和【k4t-dns】。

### 生产级别-最佳实践：总共3台虚拟机。

* 在linux + zabbix中，安装powershell和【k4t 监控端】。
* 1台虚拟机中，安装alpine-linux，挂载数据盘并安装【k4t master】。
  使用时，用alpine-linux的iso启动。执行完【k4t master】任务，就关机。
  需要再次执行【k4t master】任务时，由【k4t 监控端】，执行开机命令开机。
  alpine-linux开机时间10---15秒。这样节省云机子费用。
* 2台虚拟机中，安装【k4t网关】，和【k4t-dns】。起冗余高可用功能。

### 生产级别-最佳实践，的最终结论：

2台虚拟机装（k4t 网关-dns），加1台虚拟机装k4t master。总共3台。即可实现生产级别的高可用部署。

---

k4t即：《kaiiit生产级别的容器编排系统》
[https://gitee.com/chuanjiao10/k4t](https://gitee.com/chuanjiao10/k4t)

项目目前状态：

未发布，诚征vc投资中。

筹到钱后，会在【k4t官方群：722528388】内，诚征内测用户。

谢谢观看。

分类: [kaiiit即k4t](https://www.cnblogs.com/piapia/category/1921541.html)

标签: [docker](https://www.cnblogs.com/piapia/tag/docker/) , [k4t](https://www.cnblogs.com/piapia/tag/k4t/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [ps1倚天剑](https://www.cnblogs.com/piapia/tag/ps1%E5%80%9A%E5%A4%A9%E5%89%91/) , [kaiiit](https://www.cnblogs.com/piapia/tag/kaiiit/) , [船长](https://www.cnblogs.com/piapia/tag/%E8%88%B9%E9%95%BF/) , [最佳实践](https://www.cnblogs.com/piapia/tag/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5/) , [国产k8s](https://www.cnblogs.com/piapia/tag/%E5%9B%BD%E4%BA%A7k8s/) , [ps1绣花针](https://www.cnblogs.com/piapia/tag/ps1%E7%BB%A3%E8%8A%B1%E9%92%88/)


