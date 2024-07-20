关键字 powershell Deny Hosts Fail2Ban ssh linux

# 前 言

近期惊闻 黑客团伙利用SSH暴力破a解，入侵远程设备 用于挖矿和DDoS攻击

疑似来自罗马尼亚、至少从 2020 年开始活跃的一个黑客团伙正使用此前从未被记录的 SSH 暴力破a解（使用 Golang 编写），对使用 Linux 的设备发起加密劫持活动。在成功入侵之后，**就会部署门罗币（Monero）恶意挖矿软件。**

为了阻止此种黑客，我编写了powershell版的Deny Hosts 脚本。这是我原创的脚本，世界唯一，我以前曾经发表过此脚本。

# 正 文

#### 系统需求：

支持centos7，centos8，debian9---11，Ubuntu1604，Ubuntu1804，Ubuntu2004

#### 下载地址：

https://gitee.com/chuanjiao10/kasini3000/blob/master/node_script/bkj_linux_deny_sshhost4.ps1

或

git clone [git@gitee.com](mailto:git@gitee.com):chuanjiao10/kasini3000.git

**用法：**

1 **安装linux版powershell。一键安装地址**

**[https://gitee.com/chuanjiao10/kasini3000\_agent\_linux](https://gitee.com/chuanjiao10/kasini3000_agent_linux)**

 ****

**2 用crontab每分钟运行。**

**echo "\*  \*  \*  \*  \*  /usr/bin/pwsh -file /你的路径/bkj\_linux\_deny\_sshhost4.ps1"  >>  /var/spool/cron/root**

#### 注意：

\$ssh连接失败次数阀值 = 4

密码输入错误，一旦超过这个次数，则进入黑名单。

只需要删除 /etc/hosts.deny 文件，即可解除所有阻止的ip

谢谢使用。

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [Fail2Ban](https://www.cnblogs.com/piapia/tag/Fail2Ban/) , [Deny Hosts](https://www.cnblogs.com/piapia/tag/Deny%20Hosts/) , [ssh](https://www.cnblogs.com/piapia/tag/ssh/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [linux](https://www.cnblogs.com/piapia/tag/linux/)
