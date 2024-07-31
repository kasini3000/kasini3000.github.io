---
categories: k4t2
layout: post
permalink: /k4t2/动态负载均衡网关《envoy powershell 遥控器》项目开源了
---




# 动态负载均衡网关《envoy powershell 遥控器》项目开源了

# 简介

我的《envoy powershell 遥控器》项目开源免费了

项目名：《envoy\_powershell》

项目中文名：《envoy powershell 遥控器》

https://gitee.com/chuanjiao10/envoy\_powershell

powershell 语言编写的 envoy 控制平面。
Envoy control plane written in powershell language.

envoy是比nginx更好的负载均衡网关，istio就是基于envoy。它的特色是【动态配置】负载均衡后端。而nginx的配置变化需要狂reload。

本项目是powershell语言编写的 envoy 控制平面。

本项目通过在linux中运行一个envoy容器，和powershell脚本，

把【使用本地配置文件，来动态控制envoy】，转换成【从命令行，经过ssh协议，通过命令，控制envoy】，

以达到动态添加后端，删除后端，添加路由，删除路由的功能。

本项目暂时没有写，添加多个envoy【listeners】的脚本。因为负载均衡网关会占用多核心cpu，建议多虚拟机使用。

可以通过建立多个虚拟机，或多个目录，docker run多个envoy容器，来实现多个【listeners】副本。

![](https://img2020.cnblogs.com/blog/456691/202009/456691-20200925123207844-2066765351.png)

# **安装**

1 安装一台linux，不支持win。

2 linux一键安装powershell。

https://gitee.com/chuanjiao10/kasini3000\_agent\_linux

3用git下载本软件的所有脚本，并保存在/etc/envoy\_ps1。

4 linux上安装docker。

https://docs.docker.com/engine/install/

5 下载envoy：

docker pull envoyproxy/envoy-alpine:v1.14.4

# **使用**

问：如何设定cds？

答： cds名，就是应用名。

add-cds.ps1 名

del-cds.ps1 名

delall-cds.ps1

问：如何设定后端ip，端口？

答：

add-eds.ps1 -cname cds名 -ip 1.2.3.4 -port 80

del-eds.ps1 -cname cds名 -ip 1.2.3.4

问：如何设定路由？

答：

add-rds.ps1 -cname cds名 -prefix '/url路径' -domains '域名1','域名2'

del-rds.ps1 -cname cds名 -prefix '/url路径' -domains '域名1','域名2'

delall-rds-and-new-rds.ps1 -cname cds名  # 这相当于添加了  -prefix '/' 和 -domains '\*'

# **常见问题**

问：设定的顺序是？

答：

\*   添加顺序：应该先添加cds应用名，然后添加eds后端，最后添加http路由。

\`\`\`

ssh root@你的linux的ip pwsh -f /etc/envoy\_ps1/add-cds.ps1 -cname 'abc1'

ssh root@你的linux的ip pwsh -f /etc/envoy\_ps1/add-eds.ps1 -cname 'abc1' -ip 1.2.3.4 -port 80

ssh root@你的linux的ip pwsh -f /etc/envoy\_ps1/delall-rds-and-new-rds.ps1 -cname 'abc1'

\`\`\`

\*    删除顺序：应该先删除http路由。后删除cds应用名。

\*   后端：当cds存在时，可以任意增删后端。可以把后端删空，但不建议。当删除cds时，会删除cds所属的所有后端。

问：如何查看脚本结果，及配置文件的正确性？

答：

http://linux的ip:9901/clusters   #后端

http://linux的ip:9901/config\_dump   #配置信息

问：如何测试envoy的url跳转结果？

答：

\* 在linux机上，用curl测试，跳转后的结果。

\* 在linux机上，用curl测试，跳转前的结果。

比如 192.168.1.2:80 ------> www.baidu.com:80

线在linux上curl后面的端口（百度），然后curl前面的端口。

问：url跳转的源ip是什么？

答： 你的linux的ip

# 问：url跳转的源端口是什么？

答： 取决于你运行docker时的命令行。

下面例子中，源端口就是80

\`\`\`

docker container run -d -p 80:10000

\`\`\`

经过ssh，远程命令行的用法：

\`\`\`

ssh root@你的linux的ip pwsh -f /etc/envoy\_ps1/add-eds.ps1 -cname cds名 -ip 1.2.3.4 -port 80

\`\`\`

问：如何运行容器？

答：

\`\`\`

docker container run -d -p 80:10000 -p 9901:9901 -v /etc/envoy\_ps1:/etc/envoy envoyproxy/envoy-alpine:v1.14.4

\`\`\`

\*    envoy的lds监听，本机linux的，所有ip的，10000端口。这个没必要更改。通过容器映射后，暴露在linux机80端口。

\*    envoy的admin功能监听，本机linux的，所有ip的，9901端口。

\*  建立【/etc/envoy\_ps1】目录，并把本项目的所有powershell脚本放进去。

\*    \*\*系统需求\*\*  envoy-alpine:v1.14.4 ，不支持15及以上。15的关键字变了。

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [api网关](https://www.cnblogs.com/piapia/tag/api%E7%BD%91%E5%85%B3/) , [api gateway](https://www.cnblogs.com/piapia/tag/api%20gateway/) , [http负载均衡](https://www.cnblogs.com/piapia/tag/http%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1/) , [nginx](https://www.cnblogs.com/piapia/tag/nginx/) , [envoy](https://www.cnblogs.com/piapia/tag/envoy/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [linux](https://www.cnblogs.com/piapia/tag/linux/)
