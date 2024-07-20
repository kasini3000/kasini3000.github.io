**powershell 安装 install android 安卓 termux**

**---【一 在手机上安装termux】---**

在安卓v7 手机上安装：

[https://github.com/termux/termux-app/releases/download/v0.118.0/termux-app\_v0.118.0+github-debug\_armeabi-v7a.apk](https://github.com/termux/termux-app/releases/download/v0.118.0/termux-app_v0.118.0+github-debug_armeabi-v7a.apk)

在安卓v8及以上手机上安装：

[https://github.com/termux/termux-app/releases/download/v0.118.0/termux-app\_v0.118.0+github-debug\_arm64-v8a.apk](https://github.com/termux/termux-app/releases/download/v0.118.0/termux-app_v0.118.0+github-debug_arm64-v8a.apk)

**---【二 安装termux插件“proot-distro”】---**

运行termux后，输入命令：pkg install proot-distro

 ****

**---【三 用“proot-distro”安装 “虚拟版”debian】---**

proot-distro install debian

 **powershell 传教士 原创于 2022-11-18 博客园**

**---【四 进入debian】---**

proot-distro login debian

**---【五 在debian中安装wget】---**

apt update

apt install wget

**---【六 下载powershell】---**

wget  [https://github.com/PowerShell/PowerShell/releases/download/v7.2.7/powershell-7.2.7-linux-arm64.tar.gz](https://github.com/PowerShell/PowerShell/releases/download/v7.2.7/powershell-7.2.7-linux-arm64.tar.gz)

**---【七 解压powershell】---**

mkdir  /powershell

mv  \~/powershell-7.2.7-linux-arm64.tar.gz  /powershell

cd  /powershell

tar zxvf  ./powershell-7.2.7-linux-arm64.tar.gz

**---【八 运行powershell】---**

**./pwsh**

**\$psversiontable  #powershell命令**

**get-date**

---【九 结论】---****

安卓手机不需要root。相当于建立了一个debian虚拟机，虚拟机中有root权限和根目录。

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [install](https://www.cnblogs.com/piapia/tag/install/) , [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [安装](https://www.cnblogs.com/piapia/tag/%E5%AE%89%E8%A3%85/) , [android](https://www.cnblogs.com/piapia/tag/android/) , [安卓](https://www.cnblogs.com/piapia/tag/%E5%AE%89%E5%8D%93/)


