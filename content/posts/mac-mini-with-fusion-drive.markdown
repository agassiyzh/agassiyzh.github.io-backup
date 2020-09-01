---
title: "不拆机给Mac Mini用上Fusion Drive"
date: "2016-02-28T11:13:00+08:00"
---


![](/images/mac-mni-fusion-drive/IMG_0198.jpg)

家里的Mac Mini是2014 late的中配。硬盘1T没有定制SSD或者Fusion Drive。一直想自己改造成Fusion Drive。奈何Mac Mini被苹果上次升级后从原来的两个SATA硬盘位变成了一个PCIe和一个SATA3[^1]。而且PCIe接口的SSD相较于普通SATA接口的价格一致居高不下。保修期内又不想拆机，于是只能作罢。

<!--more-->

![](/images/mac-mni-fusion-drive/info.png)

最近mini慢得越来越让人难以忍受。想起来之前听[IT公论][itgonglun]主播Rio在播客里面介绍，他用[USB3.0][usb3]接口外接SSD，将整个OS X系统装在外置移动硬盘上，得益于[USB3.0][usb3]接口的速度提升效果还不错[^2]。

于是最近买了个USB3.0转SATA3的移动硬盘盒，用一块闲置的镁光M4 128G外接，和内置的1T HDD硬盘组了Fusion Drive。OS X运行速度提升明显（公司的iMac也用的是Fusion Drive，感受上几乎没有区别）。

这个方案有这些优点：

  1. 方便，不拆机。即使将来SSD坏了或者要换一块更大的SSD同样不用拆机。
  2. 成本低
  3. 不影响保修
  4. 得益于Fusion Drive，系统会判断哪些文件需要放在较快的SSD上面。


## Fusion Drive

组建Fusion Drive其实很简单，前提是你要会使用命令行。具体操作可以参考MacWorld的这片文章：

> [How to make your own Fusion Drive](https://www.macworld.com/article/2014011/storage-drives/how-to-make-your-own-fusion-drive.html)

## Trim enable

OS X 10.10.4开始系统中多了trimforce命令。算是Apple官方允许第三方SSD开启trim功能了。

下面命令可以实现：

```bash
sudo trimforce enable
```

参考：[osxdaily:How to Enable TRIM on Third Party SSDs in Mac OS X with trimforce](https://osxdaily.com/2015/10/29/use-trimforce-trim-ssd-mac-os-x/)

## 胶水

![](/images/mac-mni-fusion-drive/IMG_0200.jpg)

考虑到Mac mini上的USB口可能要经常插拔，会不小心带到SSD硬盘口，造成Fusion Drive两块盘数据不能同步产生故障，于是我用热熔胶枪做了固定(手残将就看吧)。

![](/images/mac-mni-fusion-drive/IMG_0199.jpg)

## Boot Camp

家里这台电脑偶尔还要用用windows[^3]，于是需要用Boot Camp装个双系统。

步骤跟正常的一样，但是在进入windows安装程序的时候，选择磁盘后会进行不下去。会出现类似“无法创建磁盘之类的提示”。在这个问题上我也困扰了好久。

解决的办法是这个时侯把ssd拔出来直到windows安装成功。然后格式化Boot Camp分出来的盘。windows安装完成后把SSD插回去。

## Time Machine

为了数据安全，及时备份。
最好是家里有NAS可以自动备份。

## 成品

![](/images/mac-mni-fusion-drive/IMG_0201.jpg)

![](/images/mac-mni-fusion-drive/deviceinfo.png)

[^1]: [iFixit: Mac Mini Late 2014 Hard Drive Replacement](https://zh.ifixit.com/Guide/Mac+Mini+Late+2014+Hard+Drive+Replacement/32815) .     [Youtube Video: PCIe SSD & flex cable install on Mac Mini (late 2014) for OS X Fusion Drive\[Teardown & Reassembly\]](https://www.youtube.com/watch?v=G3MmzNRIkJM)


[^2]: 我买的硬盘盒标称SATA3转USB3.0的传输速率达到5.0Gbps基本上已经达到了目前主流SSD速度的瓶颈了。

[^3]: 我在上面装的是windows10

[feng]: https://bbs.feng.com/read-htm-tid-8976690.html

[itgonglun]: https://ipn.li/itgonglun/

[usb3]: https://zh.wikipedia.org/wiki/USB_3.0
