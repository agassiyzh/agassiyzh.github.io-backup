---
title: Shairport new
date: 2013-12-02T18:14:13+08:00
tags: [Openwrt,Airplay,Shairport]
---

之前自己[手动编译过Openwrt的Shairport][shairport blog]包。但是遗憾得是当时用官方得Markfile只能编译通过0.05版本得代码。

看到https://github.com/abrasive/shairport 这个repo上开发挺活跃（据说是重新写了一遍）。
而且号称:

> Airtunes emulator! Note tshat version 1.0 has just emerged from complete rewrite and has been promoted to the master branch.

最近有个朋友发来Email问编译的事情。无意中发现了[shairport-openwrt][2]这个Repo，可以用这里的makefile编译到最近版本的Shairport。按照README中的说明经过一番折腾终于搞定了。

提供ar71xx版本的[下载][3]。


[shairport blog]: https://blog.yuzhuohui.info/2013/09/02/shairport-for-openwrt-ar71xx/
[2]: https://github.com/sm3rt/OpenWRT-ShairPort
[3]: https://d.pr/f/13n6
