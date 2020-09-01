---
title: "pip 更新所有包"
date: 2012-09-10T17:07:00+08:00
categories: python
---
很蛋疼，忽然想吧python中的各种包更新一下。看了pip的各种选项，没有找到更新所有包的命令。

google后找到了这么一条命令（跟我一样蛋疼的人不少Y(^_^)Y）。

	pip freeze --local | cut -d = -f 1  | sudo xargs pip install -U

用了一下提示：
> Could not find any downloads that satisfy the requirement bonjour-py in /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python
> No distributions at all found for bonjour-py in /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python

<!--more-->

猜想这个```bonjour-py```是Mac特有的在pypi中找不到的缘故。然后用

	pip freeze --local

看了一下系统中的包，发现还有很多是```pyobjc```开头的包。

于是把这些Mac特有的包过滤掉变成

	pip freeze --local | cut -d = -f 1 | grep -v '^\(pyobjc\|bonjour\)' | sudo  xargs pip install -U
	
然后我的所有包都更新了。为了方便可以加一个alias这个就随便你了。

如果你跟我一样蛋疼的话可以这么做。但是最好别啦。你在没有阅读包更新的changelog的情况下强制更新是很危险的。

我想这也是pip不提供```update-all```功能的原因吧。