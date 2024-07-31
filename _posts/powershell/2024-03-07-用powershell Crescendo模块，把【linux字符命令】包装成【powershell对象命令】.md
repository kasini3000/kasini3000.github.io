这是翻译文章，原文 ：  https://devblogs.microsoft.com/powershell/announcing-powershell-crescendo-preview-1/

## 问：powershell Crescendo模块有啥用？

答：

powershell对象命令很好。但是，linux本机命令实现不了。

linux命令具有自己独特的语法，有时包含许多子命令和参数/开关，并且通常类似于其自身的语言。可以选择利用PowerShell的技能来【包装老的字符界面命令】，或者更加精通已使用的本机命令，这不是很好吗？

作为PowerShell用户，如果这些本机命令支持PowerShell功能，例如：

* 明确的命名约定– `<span class="typ">Verb<span class="pun">-<span class="typ">Noun</span></span></span>`
* 相似用途的一致参数命名
* 输出由对象组成
* 获取命令帮助的常用方法
* 易于处理管道中的对象
* 易于使用模块共享

我们很高兴宣布**PowerShell Crescendo**的首次预览，**PowerShell Crescendo**是一个框架，可以快速地把【linux本机命令】封装成PowerShell cmdlet，输出属性，支持 `<span class="typ">Sort<span class="pun">-<span class="typ">Object</span></span></span><span class="typ">Where<span class="pun">-<span class="typ">Object</span></span></span>`过滤。 而与平台无关。win，linux通用。

# 太好啦！包装（封装）成powershell命令后，就再也不用扣字符串了！

当今许多现代的本机命令都很复杂，它们本身是*微型外壳，*具有包含子级别或子上下文的自己的*微型语言*。如果你曾经使用过 `<span class="pln">kubectl</span>`，`<span class="pln">docker</span>`或者，你所经历的执行和自动化这些命令的复杂性。`<span class="pln">netsh<span class="pun">.<span class="pln">exe</span></span></span>`

为了使本机命令具有类似于PowerShell的感觉并提供类似的体验，您可以自己重新编写该工具，或者，如果该工具使用该工具 `<span class="pln">REST</span>`，则可以使用[AutoRest](https://github.com/Azure/autorest)直接调用Web API 。这些选项效果很好，但是需要更多的开发经验，并且随着时间的推移可能很难维护。

**Crescendo提供了一些工具，可以轻松包装本机命令以获得PowerShell cmdlet的优势。**将本机命令包装到Crescendo cmdlet中可以提供参数处理，例如提示输入强制性参数和制表符完成参数值。**Crescendo cmdlet可以从本机应用程序获取文本输出，并将其解析为对象。**输出对象允许您利用所有的后期处理工具等优势，等等。`<span class="typ">Sort<span class="pun">-<span class="typ">Object</span></span></span><span class="typ">Where<span class="pun">-<span class="typ">Object</span></span></span>`

## 支持什么

Microsoft.PowerShell.Crescendo 0.4.1 Preview.1当前可从[PowerShell画廊](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Crescendo/0.4.1)下载。

支持使用Crescendo编写模块的PowerShell版本：

* PowerShell 7.0以上

可以执行Crescendo模块的受支持的PowerShell版本：

* Windows PowerShell 5.1以上
* PowerShell 7.0以上

## 下一步是什么

Preview.2的Next / Future计划将基于反馈，并包括一些正在调查的项目：

* 生成模块时创建cmdlet别名
* 研究解析手册页和文档以改进自动JSON生成

## 安装

若要创建Crescendo cmdlet，您将需要PowerShell画廊中的模块**Microsoft.PowerShell.Crescendo**。

```powershell
Install-Module Microsoft.PowerShell.Crescendo
```

## 文献资料

我们已包含 `<span class="pln">about_Crescendo</span>`，将来还会添加更多文档。确保检出模块中的Samples文件夹。

```powershell
Get-Help about_Crescendo
```

要编写Crescendo模块，您将创建一个JSON配置文件，该文件描述了如何将本机命令投影为cmdlet。您需要**Microsoft.PowerShell.Crescendo**模块来构建包含Crescendo代理cmdlet的完成模块。该**Microsoft.PowerShell.Crescendo**在PowerShell中7+模块运行。

**要使用由Crescendo构建的模块，您不需要**安装**Microsoft.PowerShell.Crescendo**。**只需下载或安装生成的模块，就可以了。**由于Crescendo构建的模块与其他模块一样，因此Windows PowerShell 5.1及更高版本支持这些模块。您的工作可以帮助社区中的其他人自动化本机命令。幸运的是，发布到PowerShell画廊时，构建和共享Crescendo模块非常容易。

有关概念和设计方法的更多信息，请参见：

* [PowerShell第1部分中的本机命令](https://devblogs.microsoft.com/powershell/native-commands-in-powershell-a-new-approach/)
* [PowerShell第2部分中的本机命令](https://devblogs.microsoft.com/powershell/native-commands-in-powershell-a-new-approach-part-2/)

## 例子

### 把apt-list封装成powershell命令

这个例子建立在一个通用的Linux打包工具之上，`<span class="pln">apt</span>`用于显示，安装和删除软件。**Verb**和**Noun**属性定义新cmdlet的名称。`<span class="typ">Get<span class="pun">-<span class="typ x-hidden-focus">InstalledPackage</span></span></span>`

Crescendo模块包括一个架构定义文件，用于帮助创建和编辑JSON配置文件。模式文件包含在模块中。在下面的示例中，模式文件被复制到具有正在开发的JSON文件的临时工作目录中。模式文件的位置在JSON配置中定义，可以位于任何位置。`<span class="typ">Microsoft<span class="pun">.<span class="typ">PowerShell<span class="pun">.<span class="typ">Crescendo<span class="pun">.<span class="typ">Schema<span class="pun">.<span class="pln">json</span></span></span></span></span></span></span></span></span>`

模式定义的通用属性：

* `<span class="typ">Verb</span>`：cmdlet动词的名称（检查有效的动词名称）`<span class="typ">Get<span class="pun">-<span class="typ">Verb</span></span></span>`
* `<span class="typ">Noun</span>`：cmdlet名词的名称
* `<span class="typ">OriginalName</span>`：原始本机命令名称和位置
* `<span class="typ">OriginalCommandElements</span>`：一些本机命令具有其他强制性开关，可在给定情况下正确执行
* `<span class="typ">OutputHandlers</span>`：输出处理程序捕获从本机命令输出的字符串，并将其转换为结构化数据（对象）。与其他PowerShell cmdlet一样，可以在管道中进一步处理此对象输出。

语法：示例代码包含 `<span class="com">// <--</span>`JSON中的注释（）。它们仅用于描述目的，应删除。

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
{
    "$schema": "./Microsoft.PowerShell.Crescendo.Schema.json", // <-- Schema definition file
    "Verb": "Get", // <-- Cmdlet Verb
    "Noun":"InstalledPackage", // <-- Cmdlet Noun
    "OriginalName": "apt", // <-- Native command
    "OriginalCommandElements": ["-q","list","--installed"],
    "OutputHandlers": [ // <-- Add string output to an object
        {
            "ParameterSetName":"Default",
            "Handler": "$args[0]| select-object -skip 1 | %{$n,$v,$p,$s = "$_" -split ' '; [pscustomobject]@{ Name = $n -replace '/now'; Version = $v; Architecture = $p; State = $s.Trim('[]') -split ',' } }"
        }
    ]
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

以下示例显示了如何使用我们在JSON文件中定义的。`<span class="typ">Get<span class="pun">-<span class="typ">InstalledPackage</span></span></span>`

```powershell
PS> Get-InstalledPackage | Where-Object { $_.name -match "libc"}

Name        Version            Architecture State
----        -------            ------------ -----
libc-bin    2.31-0ubuntu9.1    amd64        {installed, local}
libc6       2.31-0ubuntu9.1    amd64        {installed, local}
libcap-ng0  0.7.9-2.1build1    amd64        {installed, local}
libcom-err2 1.45.5-2ubuntu1    amd64        {installed, local}
libcrypt1   1:4.4.10-10ubuntu4 amd64        {installed, local}

PS> Get-InstalledPackage | Group-Object -Property Architecture

Count Name   Group
----- ----   -----
   10 all    {@{Name=adduser; Version=3.118ubuntu2; Architecture=all; State=System.String[}, @{Name=debconf; V…
   82 amd64  {@{Name=apt; Version=2.0.2ubuntu0.1; Architecture=amd64; State=System.String[]}, @{Name=base-files…
```

### 把win的ipconfig.exe,封装成powershell命令

本示例包装了Windows命令并说明了如何定义参数。除了以上示例中的属性外，该架构还支持一个部分，用于将描述性命名的参数包装和投影到新cmdlet中。`<span class="pln">ipconfig<span class="pun">.<span class="pln">exe</span></span></span>`

`<span class="typ">Parameters</span>`

* `<span class="typ">Name</span>`：显示在新cmdlet中的参数名称
* `<span class="typ">OriginalName</span>`：出现在本机命令中的原始参数名称

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
{
    "$schema" : "./Microsoft.PowerShell.Crescendo.Schema.json",
    "Verb": "Get",
    "Noun": "IpConfig",
    "OriginalName":"c:/windows/system32/ipconfig.exe",
    "Description": "This will display the current IP configuration information on Windows",

    "Parameters": [ // <-- Parameter definition
        {
            "Name":"All", // <-- Name of parameter that appears in cmdlet
            "OriginalName": "/all", // <-- Name of original parameter that appears in native cmd
            "ParameterType": "switch",
            "Description": "This switch provides all ip configuration details" // <-- Help parameter
        },
        {
            "Name":"AllCompartments",
            "OriginalName": "/allcompartments",
            "ParameterType": "switch",
            "Description": "This switch provides compartment configuration details"
        }
    ],

    "OutputHandlers": [
        {
        "ParameterSetName": "Default",
        "Handler":"param ( $lines )
        $post = $false;
        foreach($line in $lines | ?{$_.trim()}) {
            $LineToCheck = $line | select-string '^[a-z]';
            if ( $LineToCheck ) {
                if ( $post ) { [pscustomobject]$ht |add-member -pass -typename $oName }
                $oName = ($LineToCheck -match 'Configuration') { 'IpConfiguration' } else {'EthernetAdapter'}
                $ht = @{};
                $post = $true
            }
            else {
                if ( $line -match '^   [a-z]' ) {
                    $prop,$value = $line -split ' :',2;
                    $pName = $prop -replace '[ .-]';
                    $ht[$pName] = $value.Trim()
                }
                else {
                    $ht[$pName] = .{$ht[$pName];$line.trim()}
                }
            }
        }
        [pscustomobject]$ht | add-member -pass -typename $oName"
        }
    ]
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

### 从JSON配置导出代理模块

导出Crescendo JSON配置文件会生成一个PowerShell模块，该模块可以在本地，GitHub和PowerShell库上共享。Crescendo包含从JSON配置文件生成代理模块的cmdlet。`<span class="typ">Export<span class="pun">-<span class="typ">CrescendoModule</span></span></span>`

* 该**的ConfigurationFile**参数应包括名称和路径JSON配置文件。
* 该**模块名**参数包含导出的模块的名称。

```powershell
Export-CrescendoModule -ConfigurationFile .get-ipconfig.crescendo.json -ModuleName Ipconfig.psm1
```

### 使用您的Crescendo模块

使用Crescendo创建模块后，就可以像安装其他模块一样安装和导入它。请记住，无论在哪里安装Crescendo模块，您都将需要原始的本机命令。

```powershell
PS> Import-Module .ipconfig.psm1
PS> Get-IpConfig -All | Select-Object -Property ipv4address, DefaultGateway, subnetmask

ipv4address  DefaultGateway                              subnetmask
-----------  --------------                              ----------

172.24.112.1                                             255.255.240.0
192.168.94.2 {fe80::145e:1779:5cfa:c2a5%8, 192.168.94.1} 255.255.255.0
```

## 您将如何提供帮助

我们的目标是使将本机命令转换为PowerShell cmdlet并获得PowerShell提供的好处变得更加容易。我们重视您的想法和反馈，希望您能尝试一下Crescendo，然后在我们的GitHub存储库前停下来，让我们知道您想要添加的任何问题或功能。

有关**PowerShell Crescendo**问题和功能的更多信息，请参见：[GitHub上的Crescendo](https://github.com/PowerShell/Crescendo)

**Jason Helmick PowerShell程序经理**

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [Crescendo](https://www.cnblogs.com/piapia/tag/Crescendo/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/)
