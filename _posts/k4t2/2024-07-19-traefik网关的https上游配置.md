# traefik网关的https上游配置

traefik v2 https 上游 upstream 边缘 Edge Gateway 博客园 services

---【前言】---

k4t国产k8s。qq群号：722528388
为了给【国产k8s】选几款网关，我盯上了欧洲人开发的老牌网关traefik。
因为没有中文网站和社区支持，自己研究很费劲。
我把经验总结下来，给大家使用。

# ---【正文】---

---【特色&优缺点】---

\* 支持win中使用。这很好，很方便。

\* 支持http，https，发送api。

\* 支持etcd，redis，云存储等作为存储数据面。

\* 支持watch目录。

\* 动态配置文件名支持中文。扩展名支持yaml，toml。只需要把某个路由扩展名改成.txt，即代表删除此路由。

\* 动态配置文件只支持utf8，无bom头编码。支持井号开始的注释。但尽量单独一行。支持中文注释。静态配置文件不支持中文。

\* 基于分布式数据库，或者频繁复制文件，达到数据面高可用。

\* 自带dashboard。但免费版只能看，不能编辑。建议测试服打开，工作服关闭dashboard。

\* 位于欧洲，github上不接受技术支持。公司自建论坛只支持英文。论坛中文邮箱注册不上。

\* 中文网站，论坛很不活跃。讨论基本没有。难以获取支持。
我发现自从2021年中开始，网上学习研究这个网关的就开始减少了。
这也是我写本文的目的，希望本文能给这个网关注入点活力。

---【弄好的配置文件例子】---

在：
https://gitee.com/chuanjiao10/powershell/tree/master/gateway\_client/traefik

baidu.toml：本文代码。

zhengze3.toml.txt：正则替换的例子。
默认不起作用，需要把baidu.toml改名。然后把此文件改名成toml

http2http.toml: 从http路由，转http上游

openssl.conf：证书配置文件。

traefik.toml：启动用的静态配置文件。

## ---【用traefik配置边缘网关，并使用https上游】---

这里先要把 test223.com ，在host文件中指向本机。
对于win，就是：
c:\\Windows\\System32\\drivers\\etc\\host文件中写入：
127.0.0.1 test223.com

然后：

目的：所有http，都转https



```
[http.routers]
[http.routers.my-router]
rule = "Host(`test223.com`) && PathPrefix(`/baidu`)"
middlewares = "redirect2https"
entryPoints = "web"
service = "noop@internal"

[http]
[http.middlewares]
[http.middlewares.redirect2https.redirectScheme]
scheme = "https"
```

注意：上面的这段代码，不能独立工作，需要下面的代码。

目的：从https，显式，转https网址


```
[http.routers.my-router-https]
rule = "Host(`test223.com`) && PathPrefix(`/baidu`)"
service = "noop@internal"
entryPoints = "websecure"
middlewares = "https-upstream"
[http.routers.my-router-https.tls]

[http.middlewares.https-upstream.redirectRegex]
regex = "^https://test223.com/baidu$"
replacement = "https://www.baidu.com"
```



---【用openssl制作，自签名证书】---

cd a:\\traefik\_v2.9.10
openssl req -x509 -nodes -days 3660 -newkey rsa:2048 -keyout test223.key -out test223.cert -config A:\\traefik\_v2.9.10\\openssl.conf

测试：
curl.exe -kv https://test223.com/baidu
powershell v5：Invoke-WebRequest https://test223.com/baidu
powershell v7：Invoke-WebRequest https://test223.com/baidu -SkipCertificateCheck

win中，用浏览器测试：
运行certmgr.msc，把证书导入到【受信任的根证书颁发机构】。

注意，尽量使用curl，或powershell测试。浏览器有时候会缓存旧的路由，或上游。
变更后，即便重启浏览器，也还是旧路由，旧上游。需要重启win。有时候重启win，也不更新。

### ---【谢谢观看，完】---

标签: [https](https://www.cnblogs.com/piapia/tag/https/) , [traefik](https://www.cnblogs.com/piapia/tag/traefik/) , [v2](https://www.cnblogs.com/piapia/tag/v2/) , [上游](https://www.cnblogs.com/piapia/tag/%E4%B8%8A%E6%B8%B8/) , [upstream](https://www.cnblogs.com/piapia/tag/upstream/) , [边缘](https://www.cnblogs.com/piapia/tag/%E8%BE%B9%E7%BC%98/) , [Edge Gateway](https://www.cnblogs.com/piapia/tag/Edge%20Gateway/) , [service](https://www.cnblogs.com/piapia/tag/service/)
