---
categories: k4t2
layout: post
permalink: /k4t2/开源免费：分享powershell读写k8s的etcd的脚本库
---

# 开源免费：分享powershell读写k8s的etcd的脚本库


powershell etcd read write put get del kubernetes k8s 读写 博客园

# ---【前言】---

篡改记忆=写入你脑仁，这是一项可怕的技术！
它可以很容易把猫的爸爸，篡改成狗狗的儿子。让你的情人轻易地变成你的仇人！
还不明白？很明显k8s的脑仁是etcd

# ---【正文】---

#### 系统需求：
win+powershell v5.x
win+powershell v7.x
linux+powershell v7.x
支持osx
支持树莓派
支持arm cpu

支持etcd版本：
3.4.x
3.5.x

版本低的话，按照下述需要修改脚本：
gRPC gateway endpoint has changed since etcd v3.3:

etcd v3.2 or before uses only [CLIENT-URL]/v3alpha/\*.
etcd v3.3 uses [CLIENT-URL]/v3beta/\* while keeping [CLIENT-URL]/v3alpha/\*.
etcd v3.4 uses [CLIENT-URL]/v3/\* while keeping [CLIENT-URL]/v3beta/\*.
[CLIENT-URL]/v3alpha/\* is deprecated.
etcd v3.5 or later uses only [CLIENT-URL]/v3/\*.
[CLIENT-URL]/v3beta/\* is deprecated.

#### 脚本特色：
支持http，支持无账号密码。支持账号密码。

#### 脚本列表：
del-etcdkey.ps1
get-etcdhealth.ps1
get-etcdkey.ps1
get-etcdversion.ps1
list-etcdkey.ps1
put-etcdkey.ps1

get-etcdMember.ps1

下载：

## https://gitee.com/chuanjiao10/powershell/tree/master/nosql/etcd

反馈，或新需求：
QQ群号=183173532

后续会增加若干metrics监控，备份，运维脚本，或参数。
而watch-key，get key的历史记录，这些脚本有必要吗？我不知道。

轮询key的话。线程空闲后，还可以干别的活。
因为watch-key是阻塞的。watch几个key，需要用到一个线程，并且这个线程一直在空等，我看不如轮询。
欢迎入群讨论。

## ---【k8s版etcd】---

系统需求：
win+powershell v7.x
linux+powershell v7.x
支持osx
支持树莓派
支持arm cpu

支持etcd版本：
3.4.x
3.5.x

支持k8s版本：
1.17及以上

脚本特色：
不支持账号密码登录etcd。支持k8s的https私钥认证，因为这些rsa的key本身就是公私钥认证，比密码更安全。

脚本列表：
del-k8skey.ps1
get-k8shealth.ps1
get-k8skey.ps1
list-k8skey.ps1
put-k8skey.ps1

get-k8sMember.ps1

下载：

## https://gitee.com/chuanjiao10/powershell/tree/master/nosql/k8setcd

用法：
1 在win上安装powershell v7.3.x
或
在linux上一键安装powershell v7.3.x
https://gitee.com/chuanjiao10/kasini3000\_agent\_linux
因为powershell v7新增了个跳过ca认证的参数。假如写脚本去兼容psv5，写起来比较麻烦，索性暂时不支持ps v5了。

2 首先需要合并cert和私钥。在k8s的master上：

```
openssl pkcs12 -export \
-inkey /etc/kubernetes/pki/etcd/server.key \
-in /etc/kubernetes/pki/etcd/server.crt \
-out ./k8s.pfx
```

然后把【k8s.pfx】复制到任意win，linux机子上。

3 用win，linux版powershell运行：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$global:endpoints = 'https://192.168.1.2,https://192.168.1.3'
./put-k8skey.ps1 -k '窃脑宅男' -v '食/脑-丧\B' -pfx './k8s.pfx'

./get-k8skey.ps1 -k '窃脑宅男' -pfx './k8s.pfx'

$这是一台真实的k8s = ./get-k8sKey.ps1 -pfx './k8s.pfx' -k '/registry/apiregistration.k8s.io/apiservices/v1.batch'
$b = $这是一台真实的k8s | ConvertFrom-Json
$b.metadata.managedFields
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

返回：

```
manager : kube-apiserver
operation : Update
apiVersion : apiregistration.k8s.io/v1
time : 2022/7/23 7:51:06
fieldsType : FieldsV1
fieldsV1 : @{f:metadata=; f:spec=}
```

# ---【后记】---

问:为什么不用etcdctl，而要编写读写etcd的库脚本？
绕过k8s的api和授权，去读写值，无疑是最方便的。
以前我用etcdctl，但还是离不开脚本调用它。
比如shell，就需要总是调用curl和jg这两个进程。
而powershell内置的相关命令，是线程级别的，不需要总是fork进程，相当于费内存省cpu。这对于批量读写etcd，还是能省不少cpu的。
总之，etcdctl没有这个功能：把json解析成powershell哈希表对象。

etcd作为键值对数据库，值本身并不支持存储powershell对象。
而要实现“存储ps对象的值”，需要再套一个序列化脚本，这也需要写脚本。

请问，绕过k8s的api，用脚本去读写etcd的人多起来后，k8s会不会额外加密？来故意设置障碍？

有了这些库脚本，再加上【kasini3000库】，很容易实现【etcd扩容，缩容】，【etcd备份，恢复】，【从外部数据库，ssh远程给脚本传值，直接读写k8s，实现某个功能】。

最后：

我的口号是：扔掉kubectl，让powershell读写etcd，让kasini3000访问k8s脑仁！

标签: [k8s](https://www.cnblogs.com/piapia/tag/k8s/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [read](https://www.cnblogs.com/piapia/tag/read/) , [write](https://www.cnblogs.com/piapia/tag/write/) , [etcd](https://www.cnblogs.com/piapia/tag/etcd/) , [kubernetes](https://www.cnblogs.com/piapia/tag/kubernetes/) , [读](https://www.cnblogs.com/piapia/tag/%E8%AF%BB/) , [写](https://www.cnblogs.com/piapia/tag/%E5%86%99/)
