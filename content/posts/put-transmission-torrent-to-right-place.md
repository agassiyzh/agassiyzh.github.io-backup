---
title: 把Transmission下载的资源放在合适的位置
date: 2013-09-26T20:44:42+08:00
tags: [Transmission]
---

最近发现其实用Transmission下载资源还是很快的（之前喜欢用Aria2下载迅雷离线，现在作为二手准备）。

前段时间搞定了订阅RSS，自动下载自己想要的剧集，完成后发邮件通知（后面的Blog中会分享）。渐渐发现我的方案中还有一个点比较烦人，由于我用XBMC来管理播放的我这些影视节目（好处是可以用手机pad来遥控，并且字幕，视屏信息做得非常好），利用XMBC的电视削刮器要求对剧集存放的文件夹归档的井井有条。而Transmission一般都下载在同一个目录中，每次下载完要手动把文件放在该放的位置。下载的东西多了就比较讨厌。

PS：XBMC太好用了不知道的Google吧。

<!--more-->

这是手机XBMC的截图，各种信息很全

![剧集列表](https://pic.yupoo.com/agassi/DbVjR445/medish.jpg)

![选择单集观看](https://pic.yupoo.com/agassi/DbVjR8Nn/medish.jpg)

![单集信息](https://pic.yupoo.com/agassi/DbVjRcbN/medish.jpg)

![演员信息](https://pic.yupoo.com/agassi/DbVjWkp7/medish.jpg)

![控制面板](https://pic.yupoo.com/agassi/DbVjVMhU/medish.jpg)

我现在的存放结构是这样的

```
├── Books
├── Downloads
├── Movies
│   ├── Amour.2012.BDrip.XviD.AC3.MULTISUBS-XaW
│   ├── Before.Sunrise.1995.720p.WEB-DL.AAC.2.0.H.264-HDStar 
│   ├── ……
├── Series
│   ├── Adventure Time
│   │   └── Season 03
│   ├── Broke.Girls
│   │   └── Season 03
│   ├── Brooklyn
│   ├── Brooklyn Nine Nine
│   │   └── Season 01
│   ├── Downton.Abbey
│   │   └── Season 04
│   ├── Hanzawa Naoki
│   ├── Hell on Wheels
│   │   └── Season 01
│   ├── House of Cards(2013)
│   │   └── Season 01
│   ├── Masters of sex
│   │   └── Season 01
│   │       └── Subs
│   ├── Modern.Family
│   │   ├── Season 01
│   │   │   └── Subs
│   │   ├── Season 02
│   │   │   └── Subs
│   │   ├── Season 03
│   │   │   └── Subs
│   │   └── Season 05
│   ├── Sleepy Hollow
│   │   └── Season 01
│   └── The White Queen
│       └── Season 01
│           └── Subs
└── WWDC2013
```

`Downloads`是下载文件夹，`Moives`是电影目录，`Series`是剧集文件夹，`Series`按照不同的剧创建各自的文件夹，然后每个剧下面按照季分文件夹。

电视削刮器规则可以参考[[教程] XBMC中文电视剧资料库使用说明][filter]

目前国内主流的视频提供网站（比如[人人影视][YYETS]）提供的视频文件的文件名，命名还算是比较好的。应该是有一套约定俗成的命名规则。

比如：

	纸牌屋.House.Of.Cards.2013.S01E01.Chi_Eng.HR-BluRay.AC3.1024X512.x264-YYeTs人人影视.mkv

首先来判断是剧集还是电影，如果是电影的直接放在电影文件夹。

判断的规则就是类似`S01E01`这样的季号号集号。

```shell
echo $name| grep -Ei "s[0-9]+e[0-9]+"
```

然后就是把剧的英文名取出来（TVDB的削刮器不认中文的）。拼成目录的。

思路有了，一开始我用Transmission配置中下载完成的脚本来做的。下载完成后MV到正确的文件夹，但是这样做的坏处是强硬得把视频移走后就不能上传分享了（这样有违`人人为我，我为人人`的P2P精神）。

下面提供的这个脚本是利用crontab定时通过`transmission-remote`取下载的信息。发现还没有下载完成的直接用`transmission-remote --move`来修改文件下载位置（还没有下载完成的时候放在.incompleted文件夹中，减少噪音）。

脚本还有不足的地方欢迎大家指导。



{{< gist agassiyzh 6713607 >}}

[filter]: https://bbs.htpc1.com/thread-65704-1-5.html
[YYETS]: https://www.yyets.com

