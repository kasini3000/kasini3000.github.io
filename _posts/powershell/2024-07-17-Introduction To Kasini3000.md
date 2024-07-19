
# PowerShell Automation : Introduction To Kasini3000

tags: Ansible , pipeline , winrm , SSH , psremoting , automation , devops

main site: [https://gitee.com/chuanjiao10/kasini3000](https://gitee.com/chuanjiao10/kasini3000)

site mirror: https://github.com/kasini3000/kasini3000 --- Welcome give star to project

win,linux devops automation batch script framework.(It is similar to Puppet，Ansible，pipeline)
Open source, free, cross-platform

## English manual:

https://gitee.com/chuanjiao10/kasini3000/blob/master/docs/kasini3000_manual.xlsx

kasini3000_agent_linux

https://gitee.com/chuanjiao10/kasini3000_agent_linux

Shell script,one click install powershell on linux,modify sshd\_config for PsRemote.

feedback:

Submitting an issue with a github account is the easiest way to give feedback I think.

It would be better if you could join Tencent QQ group, which is an instant messaging software similar to msn.

---

# Difference between kasini3000 and ANSIBLE:

kasini3000 is script, ansible is YAML.
kasini3000 can transfer powershell objects between local and remote machines each other.

transfer powershell objects support ：win<->win,linux<->linux,win<->linux,linux<->win

Note: powershell commands in azure, aws, vmware, win node machines, will generate objects and return to the master machine.
for linux node , you need to construct the object yourself, here is an example:

c:\\ProgramData\\kasini3000\\node\_script

---

The technology, protocol, or feature keyword supported by "kasini3000":

Offline installation of win, linux, master, node and all libraries.
winrm, ssh, rsync between win and linux, batch telnet, batch sftp, batch ftp-sync, batch sftp-sync, sqlite, remote object transfer,
batch database client, picture alarm, balloon alarm, sound alarm, WeChat alarm , Dingding alarm.
Support from win, linux master sodo login to linux node.
Free support for 1 batch of linux2winrm. Another batch of linux2winrm is supported for a fee.
The remote command line from the master to node supports only bash in the Linux node, but no pwsh is installed. But this is not recommended.

### Q: I already have my .py, .sh, .pl, .bat scripts, why use your framework?

A: The framework provides these functions:
Kasini3000 provides multi-process and multi-thread engine; automatic remote transfer of ps1 scripts and code blocks;
timed task engine; remote value transfer of objects; Batch modify win node, linux node password.

---

# win install：

Remove-Item -LiteralPath 'c:\\ProgramData\\kasini3000' -Recurse -Force -Confirm:\$false
cd 'c:\\ProgramData\\'
git clone https://gitee.com/chuanjiao10/kasini3000.git
#git clone git@gitee.com:chuanjiao10/kasini3000.git
c:\\ProgramData\\kasini3000\\zkj\_install\_k记饭店\_win主控机.ps1
#script dependencies 【c:\\Users\\【your\_name】\\Documents\\kasini3000\\nodelist.csv】

linux install：

Remove-Item -Force -LiteralPath '/etc/kasini3000'
cd /etc
git clone https://gitee.com/chuanjiao10/kasini3000.git
#git clone git@gitee.com:chuanjiao10/kasini3000.git
/etc/kasini3000/zkj\_install\_k记饭店\_linux主控机.ps1

# customize nodelist.csv :

win file patch :          c:\\users\\【your\_name】\\Documents\\kasini3000\\user\_config.ps1
inux file patch :         /root/kasini3000/user\_config.ps1

\$global:被控机列表文件 = "\${global:kasini3000\_data\_path}/nodelist111.csv"
\$global:zd只读nodelist文件 = \$false
\$global:config = \$false
\$global:0k\_source = \$false

Charging plugin:

20\$  nodelist.xslx
Supports Win, Linux, and host computers. No need to install Excel.

nodelist.csv definition:

win file patch :          c:\\users\\【your\_name】\\Documents\\kasini3000\\nodelist.csv

inux file patch :         /root/kasini3000/nodelist.csv

ip,port,user,pass for win
ip,port,user for linux.
If the linux password field is not empty string, the linux password can be used to push ssh-pub-key-file
./cduuid 'c3afb273-8ab4-4ec2-8716-12a05fac1bfe'
./cdname 'the name of node'
./cdip 'the ip of node'
./cdg 'the group of node'
The password timestamp is used for batch password change script for win
node os type is an empty field. When connected to the node, its value is written into the powershell varible

You can add any custom fields to nodelist.csv. For example: region

make two ssh-keyfile for linux node :

Change one of two key in turn every 3-6 months.

run:
/etc/kasini3000/gx更新主控机ssh秘钥1.ps1
/etc/kasini3000/gx更新主控机ssh秘钥2z.ps1

created file path win:
c:\\users\\【your\_name】\\Documents\\kasini3000\\ssh\_key\_files\_old1\\id\_rsa
c:\\users\\【your\_name】\\.ssh\\id\_rsa
c:\\users\\【your\_name】\\Documents\\kasini3000\\ssh\_key\_files\_old1\\authorized\_keys

created file path linux:
/root/.ssh/id\_rsa
/root/kasini3000/ssh\_key\_files\_old1/id\_rsa
/root/kasini3000/ssh\_key\_files\_old1/authorized\_keys

on win master,push c:\\users\\【your\_name】\\Documents\\kasini3000\\ssh\_key\_files\_old1\\authorized\_keys to linux node /root/.ssh/authorized\_keys:
winscp复制主控机公钥到被控机\_win2linux\_pwd.ps1 -ipaddress nodeip -root\_password 'xxx'
winscp复制主控机公钥到被控机\_win2linux\_pwd.ps1 -ipaddress nodeip -sudo\_user 'aaa' -sudo\_password 'bbb'
or
putty复制主控机公钥到被控机\_win2linux\_pwd.ps1 -ipaddress nodeip -root\_password 'xxx'
putty复制主控机公钥到被控机\_win2linux\_pwd.ps1 -ipaddress nodeip -sudo\_user 'aaa' -sudo\_password 'bbb'

on linux master,push /root/kasini3000/ssh\_key\_files\_old1/authorized\_keys to linux node /root/.ssh/authorized\_keys:
use ssh-copy-id

Set Language to english：

set\_Language\_english.ps1

Welcome to help translate the project to English.

### setting file:

config.ps1

---

# console:

k-console.ps1 #console main program,
krun.ps1 #run kasini3000 script or file
k\_run\_ip.ps1 #run kasini3000 script or file

#goto node console,"exit" to exit ,like powershell Enter-PSSession
./gtnode

#verbose on
./v-kai开启详细信息输出.ps1

---

# choose node:

./cdall choose all node
./cduuid 'c3afb273-8ab4-4ec2-8716-12a05fac1bfe'
./cdname 'the name of node'
./cdip 'the ip of node'
./cdg 'choose a group of node'
./cdos 'win7' choose all win7
'win7','win8','win10','win11','win2008r2','win2012r2','win2016','win2019','win2022','centos6','centos7','centos8','debian9','debian10','ubuntu1404','ubuntu1604','ubuntu1804','ubuntu2004','Amazon Linux 2','kylin v10','alpine','统信UOS服务器版'

./cdwin #choose all win
./cddebian #choose all debian 9,debian10,debian11
./cdubuntu #choose all ubuntu 1604---2204

---

### 1 Remote command line：

(from win,linux master,to win,linux node)

\$object\_return\_from\_node\_to\_master = ./k\_run\_ip -ip 1.1.1.1 -scriptblock {command\_on\_node} -allparameter \$a,\$b
\$object\_return\_from\_node\_to\_master = ./k\_run\_ip -ip 1.1.1.1 -file 'd:\\script\_file\_on\_master.ps1' -allparameter \$a,\$b
\$string\_return\_from\_node\_to\_master = ./k\_run\_ip -ip 1.1.1.1 -scriptblock { bash /tmp/xxx.sh} -allparameter \$a,\$b

### 2 Multi-process batch execution:

default timeout:30min max:10day max-process:99,change from create user\_config.ps1
k-commit.ps1 -ip 1.1.1.1 -scriptblock {command\_on\_node} -allparameter \$a,\$b
k-commit.ps1 -ip 1.1.1.1 -file 'd:\\script\_file\_on\_master.ps1' -allparameter \$a,\$b
./kt.ps1 #table
./kl.ps1 #list

### 3 Multi-thread batch execution:

default timeout:10min max:1hour max-threaded:50per process,300 max limit.change from create user\_config.ps1 #need powershell module poshrsjob
There is no need to run the command k-console.ps1 before k-commit-rs.ps1 command runs
k-commit-rs.ps1 -ip 1.1.1.1 -scriptblock {command\_on\_node} -allparameter \$a,\$b
k-commit-rs.ps1 -ip 1.1.1.1 -file 'd:\\script\_file\_on\_master.ps1' -allparameter \$a,\$b
./kt-rs.ps1 #table
./kl-rs.ps1 #list
./kt-rs -ip '192.168.168.223' #select ip from sqlite
./kt-rs -remark 'xxx'
./kt-rs -begin\_time '2021-11-11 15:05:01'
./kt-rs -end\_time '2021-11-11 16:05:01'

./kt-rs -grep 'disk full'

\$global:\_mtout   #kt-rs and kl-rs output variable

\$global:\_mpout   #kt and kl output variable

foreach (\$i in \$global:\_mtout)
{
　　if (\$i['标准输出'].contains('xxx'))
　　{
　　　　'has xxx error'
　　}
}

k\_commit\_ip\_rs.ps1 # for advanced users

"-rs" on end of command is mean: "runspace" , is Multit-hreading.

kt,kl is mean: multi-Progress

### 4 From win, linux master computer ,to win linux node computer, copy files. It helps you push your scripts.

k\_copyfrom\_ip.ps1 -ip 1.1.1.1 -Path 'node' -Destination 'master' #win2linux,win2win,linux2linux
k\_copyto\_ip.ps1 -ip 1.1.1.1 -Path 'master' -Destination 'node'

### 5 Execute your script by psremoting.

100% Compatible with all scripts(.py .sh .perl or more) in win and linux.
The results are stored in the sqlite database. According to node-ip, time, query results.

./k\_run\_ip -ip 1.1.1.1 -scriptblock {

```
/path/xxx.sh
```

}

# or

k-commit-rs.ps1 -ip 1.1.1.1 -scriptblock {

```
python /path/xxx.py
```

}

6 ftp, sftp folder synchronization (win master only)

k\_sync\_ftpd2l\_win.ps1
k\_sync\_l2ftpd\_win.ps1
k\_sync\_l2sftpd\_win.ps1
k\_sync\_sftpd2l\_win.ps1
k\_download\_from\_ftpd\_win.ps1
k\_upload2ftpd\_win.ps1

### 7 The cwrsync client is encapsulated for rsync between win and linux.(win master only)

k\_rsync\_winfromlinux.ps1
k\_rsync\_wintolinux.ps1

### 8 txt2 voice reading alarm (win master only), win terminal background picture alarm.

baoj中英文声音报警.ps1
\\admin\_gui\\qwin右下角气球报警.ps1

win terminal background picture alarm:

c:\\ProgramData\\kasini3000\\admin\_gui\\pic

### 9 compress script:

c:\\ProgramData\\kasini3000\\node\_script\\7z
l7za2zip.ps1
l7za27z.ps1
l7zu2zip.ps1
l7zu27z.ps1
l7zx.ps1

w7za2zip.ps1
w7za27z.ps1
w7zu2zip.ps1
w7zu27z.ps1
w7zx.ps1

10 node mem,cpu,disk info:

“c:\\ProgramData\\kasini3000\\node\_script”  on master <----> on node “c:\\ProgramData\\kasini3000\\node\_script”

to copy node\_script folder to node:

./cdip 1.2.3.4

./kctf复制主控机node\_script目录脚本到当前被控机.ps1

### 11 find&replace tool,like linux SED:

c:\\ProgramData\\kasini3000\\node\_script\\kasini3000\\psed.ps1
manual: c:\\ProgramData\\kasini3000\\docs\\ps找算替\_v3\_readme.html

### 12 Batch password change:

12.1 Build an 18-bit random number and write it into the old password field in nodelist. csv

c:\\ProgramData\\kasini3000\\cs产生所有被控机旧密码并写入nodelist.ps1

12.2 Change the password, verify, swap the current password and old password fields, and write the password change time.

c:\\ProgramData\\kasini3000\\zkj\_s所有linux被控机改密码\_验证周期\_改密码\_验证\_对调密码\_写入时间戳.ps1  # for all linux node on this nodelist.csv
c:\\ProgramData\\kasini3000\\zkj\_s所有win被控机改密码\_验证周期\_改密码\_验证\_对调密码\_写入时间戳.ps1 # for all win node on this nodelist.csv

### 13 Install/remove packages, ignoring linux distribution installation command differences:

c:\\ProgramData\\kasini3000\\node\_script\\bkj\_remove\_linuxpackage.ps1

c:\\ProgramData\\kasini3000\\node\_script\\bkj\_install\_linuxpackage.ps1

use title 10 command ,copy up 2 files to each linux node,then:

./k\_run\_ip -ip 1.2.3.4 { /etc/kasini3000/node\_script/bkj\_install\_linuxpackage.ps1 -package\_names 'a','b','no\_this\_packagename' }

---

### from linux client ,login to windwos server ,then send command and run:

1 install omi https://github.com/microsoft/omi
or https://github.com/jborean93/omi

After install microsoft OMI or jborean93/omi , then you can connect from linux to windows by WINRM-5985 (ntlm).
but OMI has some bug ,sctipt file name on windows does not support characters other than English.

2 k\_run\_ip\_port\_l2w\_user\_pwd.ps1 -ip your\_windows\_node\_ip -port xxx -user xxx -pwd xxx {
command on windows
more command ...
}

Here is a paid plugin for \$20.
Support batch from linux to win (k-commit&k-commit-rs), support nodelist.csv

---

https://gitee.com/chuanjiao10/kasini3000/blob/master/k\_run\_ip\_port\_w2l\_user\_expect\_pwd.ps1
The script solves the following problems:
1 The ps session from win to linux does not support the problem of specifying a password to login through parameter values.
2 The ps session from win to linux does not support specifying the sudo password through parameter values, and it is necessary to manually enter the sudo password.
3 Support the original parameter transfer of pssession. pass value.
4 For a single node machine, it cannot run concurrently. Powershell v5.x is not supported

5 need powershell install on linux

usage:
\$a = 123
[scriptblock]\$b = { touch \$args[0] }
c:\\ProgramData\\kasini3000\\k\_run\_ip\_port\_w2l\_user\_expect\_pwd.ps1 -sudo \`
-ipaddress '192.168.1.2' -user 'xxx' -pwd 'xxx' \`
-file 'd:\\c.ps1' or -scriptblock \$b
-allparameter \$a

---

# Scheduled tasks on master:

c:\\ProgramData\\kasini3000\\cmdb\\u库\_触发器表\_添加触发器341.ps1 #add trigger
c:\\ProgramData\\kasini3000\\cmdb\\u库\_计划表\_添加动作331.ps1 #add action
c:\\ProgramData\\kasini3000\\cmdb\\u库\_绑定\_动作和触发器351.ps1 #Bind Trigger Action
ul.ps1
ut.ps1

---

### Standalone  script:does not depend on nodelist. csv, but relies on powershell on node

k\_copyfrom\_ip\_port\_l2l\_user.ps1
k\_copyfrom\_ip\_port\_w2l\_user.ps1
k\_copyfrom\_ip\_port\_w2w\_user\_pwd.ps1

k\_copyto\_ip\_port\_l2l\_user.ps1
k\_copyto\_ip\_port\_w2l\_user.ps1
k\_copyto\_ip\_port\_w2w\_user\_pwd.ps1

k\_run\_ip\_port\_l2l\_user.ps1
k\_run\_ip\_port\_w2l\_user.ps1
k\_run\_ip\_port\_w2w\_user\_pwd.ps1

---

### Standalone script: does not depend on nodelist. csv, does not depend on the powershell of the node computer, but depends on the winscp module on the master computer, which makes these scripts only available in win.

k\_copyfrom\_ip\_port\_winscp2bash\_user\_pwd.ps1
k\_copyto\_ip\_port\_winscp2bash\_user\_pwd.ps1
k\_run\_ip\_port\_winscp2bash\_user\_pwd.ps1 #only run one-line command

---

### devops

jenkins --->  pipeline(or script plugin)  --->  powershell or pwsh on win/linux --->  kasini3000

powershell gitlab client  ---> shellhook  --->  kasini3000

---

"kaiiit's Hotel"（kasini3000） supports multi-cloud platform use, has multiple databases client, supports aws-powershell, azure-powershell to pass objects, supports devops (as jenkins & gitlab backend) Welcome to submit translation and pr, welcome to gitee wiki to see.
