---
layout: default
categories: k4t2
---

# 网关神器traefik的，etcd和https配置例子

traefik v2 https router service etcd 边缘 Edge Gateway 博客园

原创+世界唯一声明：
本文是一个真实的例子，通过这个例子，可以配置出一个最简单的https反向代理。
本文世界唯一。我网上搜到其他的例子，都是traefik v1的。或者不是traefik搭配etcd的例子。

# ---【前言】---

写本文目的： 网上应该没有 traefik v2 配 etcd 的例子。

traefik被我喜爱的原因：跨平台，支持win，linux版同时使用。同时支持监控目录和etcd。

* 支持win，linux版同时使用。
* 支持etcd的同时，支持watch文件目录。而另一台traefik，可以配置为只用etcd。
  很方便在win上，编辑toml文件调试。然后通过dashboard查看是否成功。
  测试正常后，再向etcd中写入，以达到数据面高可用的目的。
* traefik的etcd键值设计的特别好！！！
  它用复杂的key，简单的值。
  而不是像其他任何基于eted键值的网关那样，用简单的key，复杂的值。

# ---【正文】---

## 测试版本：traefik v2.9.10 for win。原则上，linux版本一样。

### 问：手册在哪？

答：
[https://doc.traefik.io/traefik/reference/dynamic-configuration/kv/](https://doc.traefik.io/traefik/reference/dynamic-configuration/kv/)

[https://doc.traefik.io/traefik/routing/providers/kv](https://doc.traefik.io/traefik/routing/providers/kv)

### 问：要打开etcd存储，静态配置文件traefik.toml应该如何配置？

答：
[providers.etcd]
endpoints = "192.168.1.2:2379"

### 问：如何添加上游？

答：

```bash
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/services/baidushangyou/loadBalancer/servers/0/url' -v 'http://test1.baidu.com:443'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/services/baidushangyou/loadBalancer/servers/1/url' -v 'http://test2.baidu.com:443'
```

---

# 下面的例子，添加一个从 [https://test223.com](https://test223.com/) ，到https://www.baidu.com 的反向代理。

### 问：如何添加路由？

答：

```bash
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/routers/my-router-https/entryPoints/0' -v 'websecure'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/routers/my-router-https/service' -v 'noop@internal'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/routers/my-router-https/rule' -v 'Host(`test223.com`) && PathPrefix(`/baidu`)'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/routers/my-router-https/middlewares/0' -v 'https-upstream'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/routers/my-router-https/tls' -v 'true'
```

### 问：如何添加默认key？

答：

```bash
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/tls/stores/default/defaultCertificate/certFile' -v 'a:/traefik_v2.9.10/test223.cert'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/tls/stores/default/defaultCertificate/keyFile' -v 'a:/traefik_v2.9.10/test223.key'
```

### 问：如何添加中间件？

答：

```bash
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/middlewares/https-upstream/redirectRegex/regex' -v '^https://test223.com/baidu$'
./put-etcdKey.ps1 -endpoint 'http://192.168.1.2:2379' -k 'traefik/http/middlewares/https-upstream/redirectRegex/replacement' -v 'https://www.baidu.com'
```

### 问：本文涉及的，ps1脚本库在哪？

答：

[https://gitee.com/chuanjiao10/powershell/tree/master/gateway\_client/traefik](https://gitee.com/chuanjiao10/powershell/tree/master/gateway_client/traefik)

---

## 问：不知道有哪些key怎么办？ 如何获得所有key？

答：

```bash
./lib/list-etcdKey.ps1 -endpoints 'http://1.2.3.4:2379'

或

./lib/list-k8sKey.ps1 -endpoints 'http://1.2.3.4:2379'
```

---

## 问：如何从测试etcd中读数据，并写入到生产etcd？

答：

```bash
$v = ./lib/get-etcdkey.ps1 -k 'foo' -endpoints 'http://1.2.3.4:2379'
./lib/put-etcdkey.ps1 -k 'foo' -v $v -endpoints 'http://5.6.7.8:2379'
```

谢谢观看，完。
