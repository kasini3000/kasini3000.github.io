---
categories: kasini3000
layout: post
permalink: /kasini3000/用powershell实现：“倩女幽魂姥姥”版《语音报警系统》
---

# 用powershell实现：“倩女幽魂姥姥”版《语音报警系统》


# ------【第一章 前言】------

最近重温倩女幽魂，发现姥姥自带了【男女语音随机切换，说话系统】^_^。我们用powershell也很容易实现。继而实现《姥姥语音报警系统》

win7，及以上版本中，是自带语音库的，系统自带一套女声中文库，一套女声英文库。
用powershell调用，从而发音，制作报警系统。是一件太简单的事情，只需要几行代码。
\$sapi = New-Object -COM Sapi.SpVoice
\$sapi.Speak("Hello World!")
\$sapi.Speak("你好!我是计算机合成语音，能说中文了！")

Baden，powershell传教士，共同 原创文章。始于 2018-01-26 允许转载，但必须保留名字和出处，否则追究法律责任

------【第二章 正文】------

最近重温倩女幽魂，发现姥姥自带了【男女语音随机切换，说话系统】^\_^。我们用powershell也很容易实现。

1 win10-16299二周年版，自带3套语音库（中文男，中文女，英文女），也可以自己安装。
不过，控制面板中默认列出2套，需要修复注册表后，才能列出“男康康”。
导入reg，修复注册表：( 同样 适用于  win10 17663.1 三周年版 rs5 )


```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Speech\Voices\Tokens\TTS_MS_ZH-CN_KangKang_11.0]
"804"="Microsoft Kangkang - Chinese (Simplified, PRC)"
"CLSID"="{179F3D56-1B0B-42B2-A962-59B7EF59FE1B}"
@="Microsoft Kangkang - Chinese (Simplified, PRC)"
"LangDataPath"=hex(2):25,00,77,00,69,00,6e,00,64,00,69,00,72,00,25,00,5c,00,53,\
  00,70,00,65,00,65,00,63,00,68,00,5f,00,4f,00,6e,00,65,00,43,00,6f,00,72,00,\
  65,00,5c,00,45,00,6e,00,67,00,69,00,6e,00,65,00,73,00,5c,00,54,00,54,00,53,\
  00,5c,00,7a,00,68,00,2d,00,43,00,4e,00,5c,00,4d,00,53,00,54,00,54,00,53,00,\
  4c,00,6f,00,63,00,7a,00,68,00,43,00,4e,00,2e,00,64,00,61,00,74,00,00,00
"VoicePath"=hex(2):25,00,77,00,69,00,6e,00,64,00,69,00,72,00,25,00,5c,00,53,00,\
  70,00,65,00,65,00,63,00,68,00,5f,00,4f,00,6e,00,65,00,43,00,6f,00,72,00,65,\
  00,5c,00,45,00,6e,00,67,00,69,00,6e,00,65,00,73,00,5c,00,54,00,54,00,53,00,\
  5c,00,7a,00,68,00,2d,00,43,00,4e,00,5c,00,4d,00,32,00,30,00,35,00,32,00,4b,\
  00,61,00,6e,00,67,00,6b,00,61,00,6e,00,67,00,00,00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Speech\Voices\Tokens\TTS_MS_ZH-CN_KangKang_11.0\Attributes]
"Age"="Adult"
"Gender"="Male"
"Language"="804"
"Name"="Microsoft Kangkang"
"SharedPronunciation"=""
"Vendor"="Microsoft"
"Version"="11.0"
"DataVersion"="11.0.2013.1022"
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

2 编写脚本，把传入的字符串，以空格，英文逗号句号叹号等分割。
3 调用声音api，【每次！】说完半句话，（顺序）切换一下语音库。
这里面的核心内容，是切换语音库。不说废话，直接上脚本。

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
if (-not $args[0])
{
    Write-Warning '用法：'
    Write-Warning 'll姥姥语音.ps1    “我要把你嫁给，黑山老妖！我要你打碎金塔，在枉死城陪我我我我”'
    $args = @(“我要把你嫁给，黑山老妖！我要你打碎金塔，在枉死城陪我我我我”)
}

Add-Type -AssemblyName System.Speech
$sy = [System.Speech.Synthesis.SpeechSynthesizer]::new()
$sy.SelectVoiceByHints([System.Speech.Synthesis.VoiceGender]::Female)
$女 = $true
$分隔符 = "`t，。,.！ "

$n段文字 = $args[0].split($分隔符)
foreach ($1段文字 in $n段文字)
{
    $sy.Speak($1段文字)

    if ($女 -eq $true)
    {
        $sy.SelectVoiceByHints([System.Speech.Synthesis.VoiceGender]::Male)
        $女 = $false
    }
    else
    {
        $sy.SelectVoiceByHints([System.Speech.Synthesis.VoiceGender]::Female)
        $女 = $true
    }
}
$sy.Dispose()
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

win10-16299 测试通过。

用法：
ll姥姥语音.ps1    “我要把你嫁给，黑山老妖！我要你打碎金塔，在枉死城陪我我我我”
ll姥姥语音.ps1    “你姥姥我 english 也 very good”

问：linux上如何使用？
答：
linux上通过b/s，或ssh客户端等，向win远程传递事件id。
然后在win上查表id，并读对应的字符串即可。
如1001，代表“磁盘满了”。

参考资料：
https://msdn.microsoft.com/zh-cn/library/system.speech.synthesis(v=vs.110).aspx

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [sapi](https://www.cnblogs.com/piapia/tag/sapi/) , [tts](https://www.cnblogs.com/piapia/tag/tts/) , [语音](https://www.cnblogs.com/piapia/tag/%E8%AF%AD%E9%9F%B3/) , [speech](https://www.cnblogs.com/piapia/tag/speech/)

