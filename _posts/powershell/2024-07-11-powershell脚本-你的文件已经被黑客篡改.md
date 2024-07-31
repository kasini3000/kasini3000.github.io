---
categories: powershell
layout: post
permalink: /powershell/powershell脚本-你的文件已经被黑客篡改
---

本人原创powershell脚本分享。

脚本用途：列出某目录下，所有软件签名不符的文件。

系统需求： win7 + powershell 2.0 及 以上。


```
#nd你的文件已经被黑客篡改.ps1   ps1文档应保存为，含有bom头的unicode文档。
#输入一个目录，验证此目录(含子目录)下，所有文件的签名是否被篡改。
#支持 win7 + powershell 2.0及以上。
Write-Warning '你输入一个目录。本脚本将验证此目录(含子目录)下，所有文件的签名是否被篡改'
Write-Warning '注意：当杀毒软件【拦截】病毒文件时，会使本脚本运行中途卡住。'
[string]$目标目录 = Read-Host -Prompt '输入一个目录'
if (-not (test-Path $目标目录) )
{
    Write-error '无此目录，脚本退出！'
    exit 1
}

$所有文件 = (Get-ChildItem -LiteralPath $目标目录 -File -Recurse -ErrorAction 'SilentlyContinue' ).fullname

Write-Warning '下列文件已经被黑客篡改！：'
foreach ($单个文件 in $所有文件)
{
    $文件状态 = Get-AuthenticodeSignature -LiteralPath $单个文件
#QQ群号=183173532
#名称=powershell交流群
    if ($文件状态.Status -eq 'HashMismatch')
    {
        Write-Host $单个文件 -ForegroundColor Red
    }
}
```


分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [文件](https://www.cnblogs.com/piapia/tag/%E6%96%87%E4%BB%B6/) , [数字签名](https://www.cnblogs.com/piapia/tag/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D/) , [Get-AuthenticodeSignature](https://www.cnblogs.com/piapia/tag/Get-AuthenticodeSignature/) , [此数字签名无](https://www.cnblogs.com/piapia/tag/%E6%AD%A4%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D%E6%97%A0%E6%95%88/)




