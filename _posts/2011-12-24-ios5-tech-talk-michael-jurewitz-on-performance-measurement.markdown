---
layout: post
title: "ios5 tech talk michael jurewitz 谈性能测试"
date: 2011-12-24 15:18
comments: true
published: true
categories: 
---
英文原文：http://oleb.net/blog/2011/11/ios5-tech-talk-michael-jurewitz-on-performance-measurement/

这篇文章的来源是iOS5 Tech talk在柏林站的演讲。演讲者是Michael Jruewitz。

第一次翻译英文文章，欢迎提意见。

一个好的iOS app不光只是漂亮，设计优雅而已，同时它们应该启动快速，界面响应及时，并且可以有效得管理内存。在你的用户看到他之前，你应该用你的技术去判断和修复性能问题。让我们来学习一下性能损失清单并且把它应用到你的每一个应用程序中，否则你的app很可能会被你的用户扫地出门。

在这次大会中，Michael详细的讲解了如何用 Instrument来发现三种常常会遇到的性能问题。总的来说，我非常喜欢这次谈话。我回顾了之前关于Instrument的一些介绍，你必须看每个demo两三次才能入门。但是这次我清楚得感觉到我学了不少东西。

## 一般建议：

<p class="has-pullquote pullquote-adelle" data-pullquote="“最重要的测试性能问题的技术就是真正得去测试它，而不是猜测”">
猜测往往不是优化性能的真确方法。如果你没有进行测试，去优化性能将会非常困难，所以以后你必须把它放在最重要的优先级。苹果已经提供了一个 Instrument工具去量化地测量你的app的各方面。
</p>

另一个重要的方面就是一定要在真实的场景下面去测试你的系统。如果你仅仅是让一个年轻的开发者在少量的甚至是没有数据的情况下进行测试，这样你能难遇到以后会困扰到用户的性能问题和内存泄漏。你应该用真实情况下的数据量去测试你的app，并且保证要测试到最坏的情况。毕竟，那些用你的app管理着大量数据和使用最多的往往是你最好的客户，你是不会想去得罪他们的。

Michael总结了下面3个方面性能上的特性：

+ 启动快速/避免阻塞
+ 最小量使用内存
+ 高效绘图

用他的话来说，如果你优化了这些东西，那么你可能在性能良好的app的道路上前进了一大半了。

### 快速启动
<p class="has-pullquote pullquote-adelle" data-pullquote="“问一下自己，这是加载主UI基本的操作吗？如果不是，那么放在后面做吧。” ">
许多iOS app启动一次要几秒钟（即使在多任务的情况下）并且经常出现重启。没有比这更加令人沮丧的了，用户没有必要为从程序启动到可以操作等待几秒钟的时间。你的优化目标是让你的用户可以尽快操作app。这意味着一些UI组件（image或者web组件）还没有加载完是没问题的。这比让用户等到全部组件加载完成好得多了去了。
</p>
为了让启动用时最少，尽可能找出的那些你的ui准备就绪前必须调用的方法。

* application:didFinishLaunchingWithOptions:
* applicationWillEnterForeground:
* applicationDidBecomeActive:
* application:openURL:sourceApplication:annotation:
* application:didReceiveRemoteNotification:
* application:didReceiveLocalNotification:
* init
* awakeFormNib
* viewDidLoad
* viewWill/DidAppear:
* application:didFinishLaunchingWithOptions:
* viewDidLoad.

你应该注意到的最常见的问题是网络的同步请求和在一个线程中载入和解析大的数据或者图片。dispatch_async()方法是你最大的朋友。你不因该阻塞主线程的操作，因为有些同步操作可能需要花很长时间（几十秒），在程序启动阶段这个时间应该更短。尝试把这些操作放到后台队列中，当操作完成后去通知主线程队列。在那之前，你的用户界面上可以显示一个进度条。

### Time Profiler工具

![instruments-time-profiler][1]

找出这类问题可以用“Time Profiler instrument”。在你的app运行的时候， instrument会不断得采样和记录在调用堆栈中代码的当前位置，从而确定花费最多时间的方法。

当你已经收集了足够多的数据，停止录制。现在你的目标是找到调用树中最耗时的方法，并且逐一去优化它们。这里有一些Michael的建议：

* 在录制的过程中，在时间轴上放置些标记来明确一些重要的时间点，比如说启动结束的时候。
* 在查看调用树之间，先用工具栏上的按钮确定检查范围。用在录制时设定的标记来确定你想要分析哪部分的代码。
* 取消”invert call tree”和”Hide System Libraries”两个选项。前面那个使调用树更容易被人理解（即使这需要花费一点时间去点击一些没用的方法），后面那个选项可以显示系统框架的耗时。如果你隐藏它，那么你可能忽视了一个会造成你app启动时间增加的同步的网络请求。

![instruments-time-profiler-options][instruments-time-profiler-options]

* 现在是时候来探究一下调用树了。他从main()开始，通过展开你自己的树直到找你你自己的代码（通常会在大于10层的深处）并且尝试去找出那些耗时较多的方法。打开详细试图可以看到关于选中行的更多的信息，或者双击其中一个方法可以直接跳转到xcode中查看你的代码。
![instruments-timeprofiler-call-tree][instruments-timeprofiler-call-tree]
* 需要特备注意下 Self 列。它会告诉你在方法内部实际到底需要话多少时间而不是在堆栈中被这个方法调用的进行下去的时间。一个在 Self 中占用百分比比较大的往往指出了你代码中的问题，比如一个比较长的循环。
* 经验之谈：最好你的app启动的时候最多只需要2-3秒钟。如果你自己的代码在总的启动时间中占用20-30%，那么这就可能存在问题了。

## 合理使用内存

内存占用的优化是从一个开是伴随着iOS开发者的。因为iOS设备没有交换文件，所以一个app内存使用是受到硬件限制的。如果app需要的内存比可用的内存要多，那么OS只能杀掉一些app的进程，这些工作都是在后台来做的。还有一个促使我们使用更少内存的原因：如果你的app使用的内存更少，那么OS会让你的app待在后台的时间更长，那就意味着可以带给用户更好的体验。
<p class="has-pullquote pullquote-adelle" data-pullquote="在iOS5中，如果你的程序是在最上层的而我们向你发了一个内存警告，那么就意味着你的头已经被放在砧板上了。">
OS可以向一个正在使用中的app发送一个内存警告来告诉你内存使用压力。Michael指出在iOS4中引入多任务，很多app懒得去处理一个内存警告。毕竟，如果一个前台的程序当接到内存警告的时候没有做任何处理，OS是不会杀掉他的进程的，而运行在后台的一些app是首先会被杀掉的。这确实是iOS4的行为。由于这些意想不到的后果，Michael强调道Apple已经在iOS5中做了改变：“在iOS5中，如果你的app是正在使用中的app，如果我们发一个内存警告给你。这就意味这你的头已经给放在案板上了（我想这是说，OS会杀掉这个前台程序的进程而不是后台的程序进程）”。所以你最好处理好内存警告。
</p>

### Instruments: Allocations + Leaks + VM Tracker + Activity Monitor

Michael给出了他自己用 instrument工具组合来测试app的方法。 Allocations,Leaks, VM Tracker 和Activty Monitor 测量工具。对你来说很容易创建一个空白的 instrument，然后从 Library inspector 面板拖动这四个监视工具到主窗口。可以保存这个 instrument为模版，以后打开就直接可以用了。

![instruments-memory-usage][instruments-memory-usage]

Activity Monitor可以用来比较你的app的和在后台运行的app的资源使用情况。打个比方，用 Real Memory 来对app进行排序然后选择 Track inspection head。现在，当你拖动刚刚录制的时间轴箭头，你可以看到你自己的app会在这些程序中根据内存的使用量上升（或者下降）。
![instruments-activity-monitor-options][instruments-activity-monitor-options]

Allocations instruments会产生误导，因为他不会显示你的app所有的内存使用情况（只会显示malloc类型的内存分配）。但是，你仍然应该关注一下下面的几种情况：

* 一个不断增长的内存分配图表显然不是一个好现象。这通常表明有些相当严重的内存泄漏。如果你不去解决这些问题，那么你的程序可能会崩溃，因为它迟早会耗尽所有的内存。
如果你在你的app中不断重复同样的动作（例如，切换到一个画面然后回到前一个画面），你的内存使用量不应该增加。在内存分配图表中你常常会看到这样的现象,在你开始进入下一个页面的时候内存有所增加（因为你创建了一个新的视图），回到前一个页面后内存有所减少，尤其是在反复多次操作的时候。
如果你发现没有或者仅仅一部分内存被回收，这表示泄漏或者废弃了某一块内存，造成这个结果的的可能是被循环retain了。用Instrument的Heapshot可以分析和追踪这些问题。通过标记堆之间的重复动作，Instruments可以告诉你哪些对象没有被释放。查看这些列表通常可以把你引向正确的轨道。
![instruments-allocations-options][instruments-allocations-options]

* 大量的内存使用会造成系统把只读的页面从内存中去掉，因为操作系统知道这些东西可以以后从磁盘中读取。由于你的app的代码也是这些可以再次载入的只读页面的一部分，那么当内存使用量在达到极限的时候可能会在系统重新载入的你app代码的时候停顿一下。尽量去避免这样的事情出现，你可以的。
“VM Tracker，提供了你的app内存使用量最准确的图表。”

VM Tracker 可以显示你的app内存使用量最正确的统计，所以它非常有用。 “Resident Size” 和“Dirty Size”这两列可以分别被映射到磁盘和无法被回收的内存数量。根据Michael的说法， “Dirty”行和“Resident”列最真实的反映了你的app在在时间轴上的某个时间点的内存使用量。所以不要因为这个数值在这里显示的和Allocation instrument上显示得完全不一样感到迷惑。

## 高效绘图
<p class="has-pullquote pullquote-adelle" data-pullquote="“经验之谈：如果在一个屏幕上超过一半的区域用了透明层，那么滑动起来很可能变得卡了。”">
Core Animation instrument可以帮你找出你的绘图代码产生的性能问题。通过把你的layer的颜色显示出来，你很快可以发现你到底做了什么。
</p>

![instruments-coreanimation-options][instruments-coreanimation-options]

即使你的app UI全部是用UIKit来控制的，并且你也没有自己绘制任何内容，也是很有可以对性能造成明显影响的，尤其是当滚动的时候。Michael说，你应该尤其注意下面几点：

### 透明过度
绘制透明对GPU来说困难多了，所以你应该尽可能避免使用透明。当然啦，很多好的效果是需要用到透明的，比如投个阴影到边缘和圆角，但是最终你的用户会更加喜欢那些滚动起来流畅的app而不是那些只是漂亮了一点点但是滚动起来一卡一卡的app。
Michael尤其强调了在自定义的table cell中构建text Label的情况。大多情况下Label可以保持一个不透明的固定的背景色，因为当cell被选中的时候table view会自动转换背景色。如果你必须要使用透明，至少要保证两个透明的label不要重叠（这样的话GPU需要增加成倍的工作）。
Core Animation instruments会标记透明的图层为红色，不透明的为绿色。
### 绘制缩放的内容
缩放图片会造成图形系统额外的工作，我们可以通过使用正确大小的资源文件来避免。如果你的app显示的内容是从网上下载的的，那么可以通过程序下载需要显示的正确大小的图片。
Core Animation instrument会用黄色突出显示缩放过的内容。并不是所有的缩放都是不好的。Core Animation instrument也会标记许多UIKit中的元素为黄色，比如navigation bar和tab bar。 因为它们使用了拉伸的图片作为他们的背景，这是没问题的。

### 绘制像素坐标
虽然Core Graphics的坐标使用了浮点，在显示前这些坐标要映射到设备固定的像素网格上。确保你需要绘制的内容的坐标是正数的，这样可以避免在显示的时候造成重叠，不然的话不但会造成性能问题（浮点计算比较费资源），同时也会使你的app看起来很糟糕。Core Animation instrument会把没有对齐的内容标记成洋红色。



[1]: http://pic.yupoo.com/agassi/BCeqjj47/flBAA.png
[instruments-time-profiler-options]: http://pic.yupoo.com/agassi/BCeqjgaL/medish.jpg
[instruments-timeprofiler-call-tree]: http://pic.yupoo.com/agassi/BCeqjkY3/medish.jpg
[instruments-memory-usage]: http://pic.yupoo.com/agassi/BCeqjxrt/medish.jpg
[instruments-activity-monitor-options]: http://pic.yupoo.com/agassi/BCeqiKep/medish.jpg
[instruments-allocations-options]: http://pic.yupoo.com/agassi/BCeqj7if/medish.jpg
[instruments-coreanimation-options]: http://pic.yupoo.com/agassi/BCeqiVJi/medish.jpg
