---
title: "在iOS单元测试中使用NSBundle"
date: 2012-08-28T17:02:00+08:00
categories: iOS
---
最近开始整理项目的代码。找了一本《重构-改善既有代码的设计》来看。

一边整理一边写单元测试来确定新添加的方法是执行正确的。其中有一个方法死活通不过。这个方法中我使用了```NSBundle```来获取app中打包进去的资源。但是调试发现根本无法定位资源。

<!--more-->

后来在[stackoverfolow][stackoverfolow]中看到是```[NSBundle mainBundle]```这个方法出的问题。Apple官方文档中对这个方法的解释是：

>	This method allocates and initializes a bundle object if one doesn’t already exist. The new object corresponds to the directory where the application executable is located. Be sure to check the return value to make sure you have a valid bundle. This method may return a valid bundle object even for unbundled applications.
	
>	In general, the main bundle corresponds to an application file package or application wrapper: a directory that bears the name of the application and is marked by a “.app” extension.

[stackoverfolow][stackoverfolow]对这个问题提出了解决方案。

可以用

```objc
[[NSBundle bundleForClass:[myClass class]]
```

或者：

```objc
[[NSBundle bundleForClass:[self class]]
```

来替代```[NSBundle mainBundle]```。

这个看起来不错，但是我想在不是单元测试的时候用[NSBundle mainBundle]，这个时候可以参考我之前的一篇文章[用同一套代码写不同版本的客户端]()

```objc
#ifdef UnitTest
NSURL *bundleURL = [[NSBundle bundleForClass:[self class]] URLForResource:CLIENT_NAME withExtension:@"bundle"];
#else
NSURL *bundleURL = [[NSBundle mainBundle] URLForResource:CLIENT_NAME withExtension:@"bundle"];
#endif
```

[stackoverfolow]: https://stackoverflow.com/questions/3067015/ocunit-nsbundle?answertab=active#tab-top