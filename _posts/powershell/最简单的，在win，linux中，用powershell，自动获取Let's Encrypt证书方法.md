powershell传教士原创 2020-04-12，2022-05更新

Let’s Encrypt证书有效期3个月，支持泛域名【\*.你的网站.net】。支持n天内（一般10天内就够用了），用脚本自动续期。

# 简介：

这个模块支持acmev2的api，这个模块支持跨平台运行，支持linux下使用。
这个模块，可以通过http，设置下级域名（dns）来认证域名。不支持通过在httpd下存放特殊文件，来验证域名。
dns插件支持：azure，aliyun，dnspod，aws，windns，bind等

dns插件支持列表：
https://github.com/rmbolger/Posh-ACME/wiki/List-of-Supported-DNS-Providers

官网：

https://github.com/rmbolger/Posh-ACME

powershell画廊网址：
https://www.powershellgallery.com/packages/Posh-ACME

# win，linux下，用管理员安装：

Install-Module -Name Posh-ACME

用管理员权限，开启powershell执行权限：（linux跳过此步骤）
Set-ExecutionPolicy RemoteSigned -Force

# 指定从Let’s Encrypt非生产服务器获取证书，并接受协议：

Set-PAServer LE\_STAGE #设定测试服务器，可以无限次数获取证书玩。

# 指定从Let’s Encrypt生产服务器获取证书，并接受协议：

Set-PAServer LE\_PROD #设定生产服务器，限制获取证书速率。

# 从其他证书服务商，获取证书：

各家免费ssl的技术参数，和免费天数。
https://github.com/rmbolger/Posh-ACME/blob/main/docs/Guides/ACME-CA-Comparison.md

#Set-PAServer BUYPASS\_PROD buypass 生产。免费6个月，不支持通配符。
#Set-PAServer BUYPASS\_TEST buypass test

#Set-PAServer ZEROSSL\_PROD
#Set-PAServer GOOGLE\_PROD
#Set-PAServer GOOGLE\_STAGE
#Set-PAServer SSLCOM\_RSA
#Set-PAServer SSLCOM\_ECC

创建Let’s Encrypt账户：

New-PAAccount -AcceptTOS \` #接受协议，并创建账户，只需要运行一次
-Contact 'me@example.com'  #你的网站管理邮箱

# 通过dns和应答txt，来获取Let’s Encrypt证书：

New-PACertificate '\*.example.com','example.com' \` #要授权的域名
-DnsPlugin Flurbog \`#dns插件名字
-PluginArgs @{FBServer='fb.example.com'; FBCred=(Get-Credential)}

dns插件支持列表：
https://github.com/rmbolger/Posh-ACME/wiki/List-of-Supported-DNS-Providers

#原理：它通过某个dns插件来工作。支持阿里，腾讯，dnspod等。
#通过dns插件，得到dns管理权限，通过命令，去dns上设置个【uuid类似】的特殊子域名，来证明你拥有域名的管理权限。
#所以这里，需要输入你的dns管理员，账户和密码。

# 得到的证书，的存储路径：

Windows：
%LOCALAPPDATA%\\Posh-ACME

linux：
\~/.config/Posh-ACME

# 证书文件说明：

cert.cer（Base64编码的PEM证书）
cert.key（Base64编码的PEM私钥）
cert.pfx（带有证书+密钥的PKCS12容器）
chain.cer（带有颁发CA证书链的Base64编码的PEM）
fullchain.cer（带有cert + chain的Base64编码的PEM）
fullchain.pfx（带有证书+密钥+链的PKCS12容器）

# 输出，得到的证书，详细信息：

Get-PACertificate | Format-List

# 续订证书：

Submit-Renewal

# centos先更新根证书：

yum install ca-certificates
update-ca-trust

注意：

关于，用网站文件方式，验证域名。

本软件或模块，不支持用【网站文件方式】验证域名。

另外听闻，使用这种方式，不允许授权 带 \* 的泛域名证书。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [证书](https://www.cnblogs.com/piapia/tag/%E8%AF%81%E4%B9%A6/) , [Let's Encrypt](https://www.cnblogs.com/piapia/tag/Let%27s%20Encrypt/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [linux](https://www.cnblogs.com/piapia/tag/linux/)
