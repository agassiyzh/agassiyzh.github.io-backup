---
title: "xcode在编译时带上git版本信息"
date: 2012-03-30T16:56:00+08:00
categories: [Apple开发]
tags: [xcode,git]
---
![](https://pic.yupoo.com/agassi/BR0aLf43/doX6m.png)

上图的这个版本信息不是我手工加的，而是xcode编译的时候自动生成的。上图中我吧git最近得tag作为CFBundleVersion（Bulid），把最近得一个commit得SAH1作为CFBundleShortVersionString（Version）。

<!--more-->

首先我们添加一个Bulid phase：
![](https://pic.yupoo.com/agassi/BR09I5Zt/ScyD8.png)

![](https://pic.yupoo.com/agassi/BR09EFnd/FDCap.png)

然后在run script中加入下面得代码。


{{< gist agassiyzh 2250618 >}}

这样，app在正式发布打包前。git中打一个tag，那么这个tag就会最为软件的版本。

要说明的是如果tag后面又有commit，那么版本信息是

	<tag name>-<tag 后面的次数>-<commit short sha1>

如果想要把commit的次数作为CFBundleShortVersionString可以把git_version改成：

```shell
git_version=`git rev-list --all |wc -l`
```
