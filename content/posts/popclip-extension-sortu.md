---
title: PopClip 排序去重插件
date: 2013-01-24T02:05:11+08:00
tags: 
- PopClip
---

在[PopClip]冰点降价的时候入手了这个神器。本来以为不就是照着iOS实现了个功能么，入手后发现自己错了。确实非常好用。

关于这个软件[Lucifr]老师已经介绍不少了。

---

看今年wwdc sessions的时候,在402号视频`《Working Efficiently with Xcode》`中Apple的工程师介绍了用Automator创建系统服务，来给选择的文本的行进行排序和去重操作，非常实用。虽然加上快捷键这种方式已经非常完美了。

但是，既然买了[PopClip]不做点插件还是有点心里痒痒。 参考这个官方插件库[PopClip-Extensions]中的文档，把上面说的功能做了一个插件。

<!--more-->

![SortU-image](https://pic.yupoo.com/agassi/CAATrfoT/medish.jpg)

![SortUed-image](https://pic.yupoo.com/agassi/CAAOYC9w/medish.jpg)

下载地址：[SortU]

第三方插件因没有签名所以需要在终端（Terminal）输入 `defaults write com.pilotmoon.popclip LoadUnsignedExtensions -bool YES` 才可以使用。↩

<del>目前正在联系PopClip，等签名下来就可以直接安装了。</del>




[PopClip]: https://pilotmoon.com/popclip/
[Lucifr]: https://lucifr.com
[popclip-extensions]: https://github.com/agassiyzh/PopClip-Extensions
[SortU]: https://d.pr/f/huNL