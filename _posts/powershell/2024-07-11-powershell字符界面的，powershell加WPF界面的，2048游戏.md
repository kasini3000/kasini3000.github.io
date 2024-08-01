---
categories: powershell
layout: post
permalink: /powershell/powershell字符界面的，powershell加WPF界面的，2048游戏
---

# ------【序言】------

1 2048游戏，有段时间很火，我在地铁上看有人玩过。没错，坐地铁很无聊，人家玩我就一直盯着看。

2 我在电脑上找了一个，试玩了以下，没几次格子就满了。我就气呼呼的放弃了。

3 前两天看了，园友【赤木晴子梦】写的py版的2048，他文字说的很清楚，注释也很好。

很多会写代码的人，写不出简单，实用，接地气的脚本。

很多会写代码的人，说（写）不明白中国话。

我基本看懂了他说的游戏玩法和原理，当然直到现在还有点糊涂。

4 根据原理我写出了powershell版的游戏。

4.1 因为游戏原理还是有点似懂非懂，所以游戏逻辑可能是有问题的。发布了测试版，请大家帮我试试，反馈意见。

4.2 py2048游戏，使用了2维数组。但我在powershell版的游戏中没使用，因为我不想使用。我有我的想法，并不是powershell不能用2维数组。powershell基于.net，2维数组还是太有了。

4.3 我不懂python，源游戏逻辑又是操作2维数组，不想研究了，基本不懂他的代码。但是游戏逻辑基本懂了后，我就用自己的想法，写出自己的游戏处理算法。

4.4 powershell和python语法都很简单，类似。即使不懂游戏算法，基本上也是可以逐条克隆语句，来达到克隆整个脚本（游戏）的目的。

4.5 此游戏在win7+powershell2.0下，win10下，简单测试过。应该说支持winxp，win7，win8，win10等。

5 通过2048游戏，对比这两种语言：
powershell本身就是命令行。自带输出字符串颜色控制，清屏，控制屏幕大小，窗口标题这些功能。用之就比python方便。

对于powershell而言，编写字符界面的脚本难，编写图形界面的简单。图形界面的游戏无需数字，字符串来回转换，来搞对齐。

# ------【游戏】------

2016-05-29 脚本更新为 1.07测试版，修复程序逻辑中的性能问题，增加游戏最高纪录功能。（目前我个人最高分为5000多分）
2016-06-01 脚本更新为 1.08测试版，修复游戏不正确的结束问题，增加无需按回车的功能。（目前我个人最高分为6800多分）

2017-03-24 更新为 1.10 版，win ,linux通用。

[https://files.cnblogs.com/files/piapia/2048game\_1.10.zip](https://files.cnblogs.com/files/piapia/2048game_1.10.zip)

![](https://images2015.cnblogs.com/blog/456691/201605/456691-20160526172100272-873236038.png)

![](https://images2015.cnblogs.com/blog/456691/201703/456691-20170324122612205-207929571.jpg)

--------------------------------分割线-----------------------------------

2016-05-31  迎六一，群友的qq为 Brightblade(66728029)，分享了由上述代码修改的 powershell + wpf图形界面的2048游戏。感谢分享！

![](https://images2015.cnblogs.com/blog/456691/201605/456691-20160531203232727-447850282.jpg)

下载地址：

[https://files.cnblogs.com/files/piapia/powershell\_wpf\_game2048.zip](https://files.cnblogs.com/files/piapia/powershell_wpf_game2048.zip)

分类: [powershell软件发布](https://www.cnblogs.com/piapia/category/420582.html)

标签: [powershell](https://www.cnblogs.com/piapia/tag/powershell/) , [2048](https://www.cnblogs.com/piapia/tag/2048/) , [game](https://www.cnblogs.com/piapia/tag/game/) , [游戏](https://www.cnblogs.com/piapia/tag/%E6%B8%B8%E6%88%8F/) , [字符](https://www.cnblogs.com/piapia/tag/%E5%AD%97%E7%AC%A6/) , [wpf](https://www.cnblogs.com/piapia/tag/wpf/) , [linux](https://www.cnblogs.com/piapia/tag/linux/) , [命令行](https://www.cnblogs.com/piapia/tag/%E5%91%BD%E4%BB%A4%E8%A1%8C/)
