# 用powershell开发跨平台动态网页

powershell 动态 网页 跨平台 asp.net dynamic cross platform

powershell 传教士 原创文章。始于 2023-04-03 允许转载，但必须保留名字和出处

# ---【前言】---

以【vbs，和微软jsript】为核心的asp已经淘汰了。ie11后来都不支持网页内嵌vbs了。
asp前后端不分离，jscript非正宗javascript标准，导致兼容性不佳。又没有大厂支持，连微软都不支持了。
asp可以被理解为【瘸腿的沙发，废弃的破烂，没元件件可更换的坏相机】

从.net跨平台那天起，asp.net就能开发跨平台动态网页。在asp.net中执行powershell代码也是早就有的，但那需要写c#代码。
在微软眼中，用c#写asp.net永远是正宫。他们不会主流去支持【用ps做动态网页开发语言】。
但我们玩powershell的是想以ps为主。我们不会用，也不想用c#去开发网站后台。

看到python家开发动态网站，我是有些羡慕嫉妒恨的。不过这种情况结束了。近期和外国同行讨论，一起研究出了示例代码。p老爷的这条瘸腿被接上了！！！

本例将为您展示：
1 在win，linux版powershell v7.3中，用powershell加载win，linux版asp.net库。
2 用powershell动态返回一个值，交给前端javascript去美化，去渲染页面。
------ 用纯powershell脚本&代码作为网页动态语言的例子。

---【图例：全部脚本代码，和相关库】---

![](https://img2023.cnblogs.com/blog/456691/202304/456691-20230406184825586-759712793.gif)

## https://gitee.com/chuanjiao10/kasini3000web

在win中运行服务器，并使用chrome浏览器测试通过。
在linux中运行服务器，并使用curl测试通过。

---【正文】---

使用场景：
\* 运维，管理员用的中小型网站，dashboard。
\* 按某按钮后，用powershell代码，加win语音库，朗读中英文文字，单词。
\* 展示表格。
\* 输入某些值，并用后台powershell，调用数据库客户端，把刚才的数据，写入单机库sqlite，litedb，excel文件，mysql库，sqlserver，etcd库等。
\* powershell调用公有云命令行，私有云命令行，建立删除云虚拟机。网络。
\* powershell调用ad，exchange，管理账户，邮箱，dns等。

案例：
目前我用它开发kasini3000的web框架。
框架还没开始，目前只是简陋的例子，勿喷。

系统需求：
powershell v7.3.x。
目前暂时只支持这个版本，因为asp.net类库我目前下载的都是.net7版本的。
未来绝对会升级到.net8的，届时需要使用powershell v7.4.x，以便ps版本和.net类库版本一致。

# ------------------【开始讲解例子】------------------

1 制作首页。首页上有个web form，上面有个post方法的按钮。html代码：

```
<form action="/getdate/" method="post">
<input type="submit" name="Logon" value="按此" />
```

2 首页，powershell代码：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$主页代码 = {
    param($HttpContext,$pagefile2)
    $pindex = Get-Content -Path $pagefile2 -Raw
    $a = [System.Text.Encoding]::UTF8.GetBytes($pindex)
    $HttpContext.Response.Body.WriteAsync($a, 0, $a.Length).AsTask().Wait()
}
$主页委托 = [powerShellDelegate]::new($主页代码,'index.html')
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

3 get-date命令，powershell代码：

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$获取时间代码 = {
    param($HttpContext)
    $d = Get-Date -Format F
    $a = [System.Text.Encoding]::GetEncoding(936).GetBytes($d)
    $HttpContext.Response.Body.WriteAsync($a, 0, $a.Length).AsTask().Wait()
}
$获取时间委托 = [powerShellDelegate]::new($获取时间代码,'')
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

4 把首页添加到路由。把get-date命令，添加到路由【/getdate】。
注意：首页是get方法，get-date是post方法。powershell代码：

```
[Void][Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions]::UseEndpoints($App, { [Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions]::MapGet($args[0],"/", $主页委托.Invoke) })
[Void][Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions]::UseEndpoints($App, { [Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions]::MapPost($args[0],"/getdate/", $获取时间委托.Invoke) })
```

# ---【后记】---

当然，你还可以这样： 写一个d:\\xxx\\a.ps1作为回调脚本。
当每次访问http://127.0.0.1:5000/a 时， 执行此脚本。

我脑子中只有一件事：替“潘而少”老爷把，【网页动态语言】这条瘸腿接上。

-完-

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [跨平台](https://www.cnblogs.com/piapia/tag/%E8%B7%A8%E5%B9%B3%E5%8F%B0/) , [动态](https://www.cnblogs.com/piapia/tag/%E5%8A%A8%E6%80%81/) , [网页](https://www.cnblogs.com/piapia/tag/%E7%BD%91%E9%A1%B5/) , [asp.net](https://www.cnblogs.com/piapia/tag/asp.net/) , [dynamic](https://www.cnblogs.com/piapia/tag/dynamic/) , [cross platform](https://www.cnblogs.com/piapia/tag/cross%20platform/)
