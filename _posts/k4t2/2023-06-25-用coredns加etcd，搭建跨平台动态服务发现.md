---
layout: default
categories: k4t2
---

coredns ddns 服务发现 动态 service discovery

2023-0625 第一版


# ---【前言】---
coredns被我喜爱的原因：跨平台，支持win，linux版同时使用。同时支持配置文件和etcd。
我用它来搭建动态服务发现。

coredns下载：内含win，linux版
https://github.com/coredns/coredns/releases


相关下载：
https://gitee.com/chuanjiao10/powershell/tree/master/ddns_server/coredns
cdns11.ps1=第1个dns启动脚本（win）
cdns11.txt=第1个dns配置文件
cdns12.ps1=第2个dns启动脚本（win）
cdns12.txt=第2个dns配置文件
启动脚本，主要解决win中，无法动态加载配置文件问题。


# ---【正文】---

设计：
1 用2个coredns，做ddns服务器，以达到高可用的目的。每个coredns，都是主dns。

2 用脚本去更改cdns11.txt，cdns12.txt，添加dns域（zone）。
注意修改2个txt，应该有一定间隔秒。让2个dns不要同时更新。
例：把下列内容加入txt文件。

```
etcd a.k4t {
endpoint http://192.168.1.2:2379
}

```

3 在etcd上添加ddns的记录：

假如域名是a.k4t,则etcd的key为:'/skydns/k4t/a'。
而'/skydns/k4t/a/c2/d' 都为下1级a记录。


```
etcdctl put /skydns/k4t/appname/ '{"host":"1.2.3.4","ttl":60}'
put-etcdkey.ps1 -endpoint 'http://192.168.1.2:2379' -k '/skydns/k4t/a/' -v '{"host":"1.254.1.1","ttl":60}'
put-etcdkey.ps1 -endpoint 'http://192.168.1.2:2379' -k '/skydns/k4t/a/c2/d' -v '{"host":"1.254.1.35","ttl":60}'
# 先用 add-etcdlease.ps1 -endpoints 'http://192.168.1.2:2379' -ttl 255 得到租约id
put-etcdkey.ps1 -endpoint 'http://192.168.1.2:2379' -k '/skydns/k4t/a/c2/d' -v '{"host":"1.254.1.35","ttl":60}' -leaseid 'iiiiidddddd'
del-etcdkey.ps1 -endpoint 'http://192.168.1.2:2379' -k '/skydns/k4t/a/c2/d'
```



"host":"1.254.1.1" -----A记录
"host":"2004:a25c::1:1" -----AAAA记录
"host":"abc.def.g" -----cname记录

4 其他：
cache=代表给这个zone添加dns缓存。
loadbalance=洗牌，随机返回ip作为优先返回。



5 测试：
win ,powershell：
Resolve-DnsName a.k4t -server 127.0.0.1

linux：
dig a.k4t @127.0.0.1


---谢谢观看，完---

