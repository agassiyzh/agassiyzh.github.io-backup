---
title: "用同一套代码写不同版本的客户端"
date: 2012-07-02T18:13:00+08:00
categories: xcode
---

最近公司的项目，需要对不同的攻略做定制版本。功能和主客户端差不多。开始想到的做法是人肉复制工程文件夹。然后把原来的项目改成需要的方式。但是这样很难做到多个客户端一起维护。要实现一个新功能需要在不同的项目中重复编码。非常之乏味。

这种场景最常见的是pro版和lite版。还有iPhone/iPod Touch版和iPad版。

这种情况下可以用xcode的target来解决。

在不同的target中设置不同的预处理宏，然后在代码中根据宏来编码。

<!--more-->

----

## 创建新的target

创建新的target方式有两种：

1. __在原有的target的基础上创建一个target__

![duplicate target](https://pic.yupoo.com/agassi/C5olB7VL/medish.jpg)

选择在工程文件在原来的的target上右击，在弹出菜单中选择```Duplate```(或者选中需要复制觉得target按⌘+D)。

![need iPad](https://pic.yupoo.com/agassi/C5olBgIi/medish.jpg)

然后有一个弹出窗口，让你选择是否创建为iPad的target。如果不需要选择Duplicate only。

注意：如果要给新生成的plist改名，还需要修改“bulid setting”中的Info Plist file一项。

2. __创建一个新的target__

![Add target](https://pic.yupoo.com/agassi/C5AFn0W4/medish.jpg)
选择Add target,就可以创建一个新的target了。

## 设置宏

![Macros](https://pic.yupoo.com/agassi/C5AFn8n5/medish.jpg)


给不同的target添加不同的预处理宏，这样就可以给不同的target添加不同的实现了。

在“bulid setting”中给Preprocessor Macros添加随意字段。

比如我们给pro版本添加“PRO”，给Lite版本添加“LITE”。 __注意:Debug和Release都要添加__

## 根据宏，为不同的target编码

```objc

 #ifdef PRO //如果是完整版
 NSlog("this is pro version");
 #endif
 
// 或者
 
 #ifndef PRO //如果不是完整版
 ...
 #else
 ...
 //这段是lite版的
 #endif
```

注意在编码和编译的时候选择需要的target

![select target](https://pic.yupoo.com/agassi/C5AFnekO/medish.jpg)

如果选择的是PRO那么在编码的时候LITE段中的代码是不会又提示的。事实上这个时候XCode是忽略这部分代码的。


![example](https://ww4.sinaimg.cn/large/6f995f46gw1dukwnndh9qj.jpg)

example: [https://github.com/agassiyzh/Diff-target](https://github.com/agassiyzh/Diff-target)
