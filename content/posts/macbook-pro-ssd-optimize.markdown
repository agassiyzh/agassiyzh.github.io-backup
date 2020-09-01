---
title: "Macbook pro SSD 优化小结"
date: 2011-12-19T13:41:00+08:00
categories: [Mac]
---

[公司全体员工集体给Macbook pro换上了ssd](https://www.v2ex.com/t/23709)型号是镁光 M4 128G。

![SSD](https://pic.yupoo.com/agassi/BBudd6Ir/medish.jpg)

虽然，我越来越信奉：“优化的第一原则就是：Do't”这句话。

但是，ssd我还是第一次用，对我来说是个新鲜物什。所以w难免要折腾一翻。Google了下"[Mac ssd 优化](https://www.google.com/search?q=mac%20ssd%20%E4%BC%98%E5%8C%96)"，参考了下面几篇文章：

* [Mac OS X 下与 SSD 相关的优化](https://blog.jjgod.org/2010/04/17/macosx-ssd-tweaks/)

* [Mac下优化SSD](https://davidx.me/2011/09/24/optimize-ssd-on-mac/)

* [纠结ssd寿命的看这里](https://bbs.weiphone.com/read-htm-tid-2499589.html)

* [分享下 SSD for Macbook Pro 的优化](https://hi.baidu.com/omys/blog/item/67fc8a0e3a7d84fd37d1220b.html)

<!--more-->

## 性能优化

### 关闭紧急运动传感器(Sudden Motion Sensor)

这个功能很想Thinkpad下面的APS，对普通硬盘还是很有必要的。但是都固态了就完全没有必要了。

可以通过下面这条命令禁用：<code>$ sudo pmset -a sms 0</code>

### 四开三关Trim

![Trim2](https://pic.yupoo.com/agassi/BBtg2JqH/medium.jpg 160)

这是一个比较纠结的问题,据[@xinquan](https://weibo.com/xinquan)说，他用Intel G2的时候，这个功能是系统默认开启的。但是当我们换上镁光M4，Lion下并没有启用。

网上有一个小软件[Tril enabler](https://www.groths.org/?p=308)可以强制开启。但是这个东西网上现在众说纷纭。

我第一次开启感觉删除东西明显变慢了，偶尔有卡顿的感觉。开机从一朵半变成了5朵。

最后在[Trim enabler](https://www.groths.org/?p=308)的留言中我找到了这么一句话：

<blockquote>
<p>TRIM is ALWAYS preferred over Garbage Collection and will likely yield better results. If you have garbage collection, you don’t necessarily need TRIM, but it’ll probably offer better performance and there’s always a chance there’ll be a degradation of speed over time. Therefore, when using Garbage Collection, you may have to take the drive out and do a secure erase so that everything is re-marked as free space.
</p>
<p>
Running garbage collection and TRIM at the same time is NOT a problem. They’re designed to work together. You can look at it like so: TRIM is called by your OS each time space is freed up. Garbage Collection is run by the drive’s firmware when idle and determines which parts of the drive can be cleaned up and rearranged. TRIM is obviously more efficient and obviously the drive’s firmware is aware of the TRIM commands and accounts for this.
</p>
<p>All SSD manufacturers, GC or not, recommend the use of TRIM.</p>
</blockquote>


作者提供了用命令行开启Trim支持的方法：

首先是备份你的硬件信息
```bash
sudo cp /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS IOAHCIBlockStorage /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage.original
```

打开Trim支持
```bash
sudo perl -pi -e 's|(\x52\x6F\x74\x61\x74\x69\x6F\x6E\x61\x6C\x00{1,20})[^\x00]{9}(\x00{1,20}\x51)|$1\x00\x00\x00\x00\x00\x00\x00\x00\x00$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
```

清楚系统内核缓存
```bash
sudo kextcache -system-prelinked-kernel
sudo kextcache -system-caches
```


最后重启

如果你需要禁用Trim：
```bash
sudo perl -pi -e 's|(\x52\x6F\x74\x61\x74\x69\x6F\x6E\x61\x6C\x00).{9}(\x00\x51)|$1\x41\x50\x50\x4C\x45\x20\x53\x53\x44$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
sudo kextcache -system-prelinked-kernel
sudo kextcache -system-caches
```
如果出错，可以恢复备份的系统硬盘信息文件：
```bash
sudo cp /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage.original /System/Library/Extensions/IOAHCIFamily.kext/Contents/PzlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
```

### 用noatime方式挂载系统盘
<blockquote>
用 noatime 方式挂载系统盘，这样可以减少不必要的 I/O 次数，虽然 SSD 做这些操作非常快速，但考虑到最后访问时间这个属性其实很少用到，大家关心的一般都是最后修改时间和创建时间，所以完全可以关闭这个属性，这在 Unix/Linux 下是非常常见的文件系统优化选项。
</blockquote>

在```/Library/LaunchDaemons```先面创建一个```noatime.plist```,内容为：

{{< gist agassiyzh 1495283 >}}

记得修改权限：
```bash
$sudo chown root:wheel /Library/LaunchDaemons/noatime.plist 
```

查看是否生效，可以输入下面的命令:

```bash
mount | grep " /
```

返回的结果应该是

```bash
/dev/disk0s2 on / (hfs, local, journaled, noatime)
```

 注意有```noatime```了。

### RamDisk

这个我现在还没有去碰。以前在win下搞过。蛋疼的时候再搞搞吧。

## 榨干硬盘空间

用了这么多年320G的硬盘，突然变成了128G，心理上感觉突然少了很多。

### 关闭休眠

<blockquote>
默认的在 MB/MBP 上, 系统会在磁盘上维护一个和内存等大的 sleepimage 文件, 当电量耗尽时将内存中所有数据写入磁盘, 系统进入深度休眠状态, 下次唤醒时再从磁盘文件恢复状态. SSD 寸土寸金, 保留一个 8GB 大小又很少被用到的文件是很奢侈的行为.
</blockquote>

{% highlight bash linenos %}
$ sudo pmset -a hibernatemode 0

$ sudo rm /var/vm/sleepimage*
```

### 关闭Time Machine本地备份

我在看容量的时候发现backup占了14G之多。我很奇怪我是把time machion在做移动硬盘上的。为什么备份会占用磁盘空间呢。google后发现我不知道怎么回事开启了本地备份功能。可以关闭本地备份来释放所占用的空间。

下面的命令可以做这件事：
```bash
$sudo tmutil disablelocal
```

### 清理垃圾

跟[@xinquan](https://weibo.com/xinquan)在Mac app store上买了个[Disk Diet](https://itunes.apple.com/cn/app/disk-diet/id445512770?l=en&mt=12)。清理垃圾还是很方便的。

![](https://pic.yupoo.com/agassi/BBtg3niP/medish.jpg)

## 拆下来的硬盘怎么办？
我们都买了硬盘盒（ORICO 2598US），样子不错银白款的盒mac比较搭。重点是拆卸方便。

![](https://www.orico.com.cn/images/20117122844739.jpg)

## 关于双硬盘 2011-12-21 1:59更新
[@Sivan](https://www.v2ex.com/member/Sivan)同学问我，如果是双硬盘，光驱位上装的是HDD怎么办，时候该开启紧急运动传感器。

[@ywjno](https://www.v2ex.com/member/ywjno)，光驱位是没有紧急运动传感器的。同时给了一篇比较专业的文章：[干掉光驱、拥抱 SSD](https://apple4.us/2011/03/kill-cdrom-embrace-ssd.html)。
