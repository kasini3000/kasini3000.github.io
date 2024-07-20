# ---【前言】---

关键字 hcloud 华为 命令行 linux windows powershell

前些天，大家因为华为云，是否应该默认开启端口，大家吵起来了，所以我抽空写了此文。解决问题，缓解矛盾。

端口默认全开是方便，但不安全。默认关了，用起来就很不方便。是开是关？问题两难。

端口使用前，通过华为云cli打开，用完后就关闭端口（即删除端口组内的一条规则）。

# ---【正文】---

### ---【1建立iam账户，授权，下载账户密码】---

每个云都需要做这一步，步骤同小异。这里就不讲解了。看后记中的iam网址操作即可。

### ---【2把账户密码，写入配置文件】---

用此命令即可，不详细说：hcloud configure init

### ---【3管理安全组】---

列出安全组：

hcloud.exe VPC ListSecurityGroups

建立安全组：

hcloud.exe VPC CreateSecurityGroup --security\_group.name="kkk"

建立后，默认会增加一堆规则。

删除安全组：

hcloud.exe VPC DeleteSecurityGroup  --security\_group\_id="11111-42c6-4caf-9485-222222"

### ---【4管理安全组规则】---

查看某安全组内安全规则：

hcloud.exe VPC ShowSecurityGroup --security\_group\_id="11111-42c6-4caf-9485-222222"

添加安全组规则：

hcloud.exe VPC CreateSecurityGroupRule \`

--security\_group\_rule.security\_group\_id="11111-42c6-4caf-9485-222222" \`

--security\_group\_rule.direction="ingress" \`

--security\_group\_rule.ethertype="IPv4" \`

--security\_group\_rule.protocol="tcp" \`

--security\_group\_rule.port\_range\_min="3390" \`

--security\_group\_rule.port\_range\_max="3390"

删除安全组规则：

hcloud.exe VPC DeleteSecurityGroupRule "11111-42c6-4caf-9485-222222"

# ---【结论：】---

华为云cli还不完美，还有很多改进的地方。

我建议：

1用华为cli，建立安全组时，不要附赠n条规则。

2貌似不能通过华为cli，绑定虚拟机实例。

3 我想要用华为cli管理nat，目前还没这功能。建议早些实现。

公网NAT网关（Public NAT Gateway）https://www.huaweicloud.com/product/nat.html

---【赠送章节】---

**建议使用win，linux通用的，powershell管理华为云，阿里云。处理返回的json结果简单**

**代码：**

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$a = d:\huawei_go\hcloud.exe  `
  vpc listvpcs `
  --region="cn-south-1" `
  --access-key="xxxxx" `
  --secret-key="yyyyyy"


$h = $a | ConvertFrom-Json -AsHashtable
$h
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

 ******图：**


 ****

 ****

**在win中用命令，一键安装powershell v7.x的最新稳定版:（目前最新稳定版是 v7.1）**

**iex  "& { \$(irm https://aka.ms/install-powershell.ps1) } -UseMSI -Quiet"**

 ****

**安装linux版powershell。一键安装地址**

[**https://gitee.com/chuanjiao10/kasini3000\_agent\_linux**](https://gitee.com/chuanjiao10/kasini3000_agent_linux)

 ****

# 我吐槽：

阿里云，华为云，不【基于powershell打造cli云命令行】，是一种歪门邪道，是一种堕落的行为。

**基于powershell做，可以实现：**

**1 win，linux，mac通用。安装简单。**

**2 长参数自动补全。而不需要为补全编写任何代码。如**-security\_group\_rule.port\_range\_max

**3 返回的json，可以在内部转换为对象和属性。如**

```
$所有华为vpc =  get-vpc实例 -region "cn-south-1"
$所有华为vpc[0].内网ipv4
```

**这样的话，多方便啊？**

# ---【参考链接】---

hcloud.exe  下载地址： [https://support.huaweicloud.com/qs-hcli/hcli\_02\_003.html](https://support.huaweicloud.com/qs-hcli/hcli_02_003.html)

华为iam  [https://console.huaweicloud.com/iam/](https://console.huaweicloud.com/iam/)

Api参考：  [https://apiexplorer.developer.huaweicloud.com/apiexplorer/overview](https://apiexplorer.developer.huaweicloud.com/apiexplorer/overview)
