---
title: iOS开发工具：自动生成@2x,@3x图片
date: 2015-04-25T16:16:00+08:00
---

自从Apple推出`iPhone 6 Plus`后为了对其适配完美在做图片的时候又多了一种尺寸也就是`@3x`的。虽然新生代神器`Sketch`可以方便存成不同尺寸的图片，但是不可否认的是Photoshop在设计师心目中的神圣地位目前还是不可撼动的。为了提高工作效率我希望有一个工具或者流程可以在设计师给我一张6倍（2和3的最小公倍数）大小的图片后自动缩放生成2和3倍大小的图片。之前想过用拖拽（Drag & Drop）的方式来实现，最近了解到OS X有Finder Action的功能可以更加方便/适合做这个事情。

<!--more-->

> Folder Actions is a feature of OS X that lets you associate AppleScript scripts with folders. A Folder Action script is executed when the folder to which it is attached is opened or closed, moved or resized, or has items added or removed. The script provides a handler that matches the appropriate format for the action, as described in this chapter.
> Folder Actions make it easy to create hot folders that respond to external actions to trigger a workflow. For example, you can use a Folder Action script to initiate automated processing of any photo dropped in a targeted folder. A well written Folder Action script leaves the hot folder empty. This avoids repeated application of the action to the same files, and allows Folder Actions to perform more efficiently.
> _from:[Apple Developer][1]_

经过一段时间的摸索，我用下面的一段脚本来满足我的需求。

解释一下下面这段用AppleScript写的脚本的作用：

> 首先可以指定一个文件夹来启用这个脚本。每当往这个文件夹中添加文件的时候脚本会被触发。脚本触发后先会检查一下添加的图片长和宽是否都是6的倍数，如果不符合会把图片放到`Error Images`的文件夹里面，并且会弹框提示哪些图片有问题（之前做了把提示朗读出来提醒设计师，感觉效果也不是很好）。如果验证通过那么会把图片压缩的两倍和三倍的大小放到`Processed Images`文件夹，文件名为也加上了熟悉的`@2x`和`@3x`。同时原图片文件会转移到`Original 文件夹`。

效果如下：

![][image-1]

### 那么问题来了，怎么做？

1. 打开`脚本编辑器`，把下面这段AppleScript脚本存到`~/Library/Scripts/Folder Action Scripts `文件夹，命名成`auto image scale`
{% gist agassiyzh/d00f6938cf5363709151 %}

2. 对需要设置的文件夹右键，选择`服务`-\>`文件夹操作设置`，然后选择刚才存的`auto image scale`。具体操作如下图
	![][image-2]

3. OK,把需要转换的图片拖进这个文件夹就可以了。

### 题外话

1. Yosemite开始OS X中AppleScript可以用Javascript来替代。但是搜索到的资料和系统的的示例代码都是AppleScript写成的。而且`脚本编辑器`中JS的文档看起来也只是AppleScript文档的一个转译。又为了了解AppleScript所以直接用AppleScript写了。以后可以尝试用JS写一下毕竟可用的三方库多很多很多。
2. 在`/Library/Scripts/Folder Action Scripts`中有一些系统预制的脚本可以参考。
3. 设置Folder的文件夹，之后不要重命名。如果有需要，重命名后重新设置一下Folder Action。
4. 设置了启用了Folder Action后会在会在`~/Library/LaunchAgents/`和生成`com.apple.FolderActions.enabled.plist`和`com.apple.FolderActions.folders.plist`两个启动plist。打开看看启示就是启动两个AppleScript。
5. `Automator`也可以用来写Folder Action。只是限制比较多，需要灵活还是要用AppleScript/JS来写。作为工程师还是直接全部用脚本来写好了。

[1]:	https://developer.apple.com/library/mac/documentation/AppleScript/Conceptual/AppleScriptLangGuide/reference/ASLR_folder_actions.html "Apple Developer"

[image-1]:	/images/auto-scale-iOS-image-folder-action/process.gif
[image-2]:	/images/auto-scale-iOS-image-folder-action/config.gif