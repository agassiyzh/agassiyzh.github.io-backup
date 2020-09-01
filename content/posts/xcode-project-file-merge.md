---
title: xcode project file merge
date: 2013-09-03T17:01:21+08:00
tags: [xcode, git]
---

在多人协作完成iOS项目的时候总是会遇到`*.pbxproj`文件冲突的情况。很不幸这个文件是那么的重要但是这次XCode5更新中又没有对其改进（xib解决得多好，期待在下个本版中又改进吧）。我有一个项目的这个文件已经达到了6w多行。

> The issue with XCode (not sure about Visual Studio) is that .pbxproj files are barely human-readable, so it doesn't make sense to resolve conflicts by hand.
> 
> _[blockquote Tom][blockquoteTom]_

就像这个@Tom老兄说的那样这个`*.pbxproj`不是给人类读的！所以手工来解决冲突太费劲了。我曾经尝试过几次。但是以后再也不会想要再试了的。

<!--more-->

那么有什么比*较好*的方法来解决这个问题呢？

在[Stackoverflow的这个回答中我找到了答案][sof]。

## 方法一

编写一个脚本:

``` shell
#!/bin/sh

projectfile=`find -d . -name 'project.pbxproj'`
projectdir=`echo *.xcodeproj`
projectfile="${projectdir}/project.pbxproj"
tempfile="${projectdir}/project.pbxproj.out"
savefile="${projectdir}/project.pbxproj.mergesave"

cat $projectfile | grep -v "<<<<<<< HEAD" | grep -v "=======" | grep -v "^>>>>>>> " > $tempfile
cp $projectfile $savefile
mv $tempfile $projectfile
```

## 方法二

在`.gitattributes`中添加下面这句:

```shell
*.pbxproj text -crlf -diff -merge=union
```


其实方法一和方法二的做法是一样的。都是单纯把merge产生的`<<<<<`,`======`,`>>>>>>`删掉罢了。只不过是第一种需要每次merge以后手工执行脚本，第二种写成了git的配置自动执行并且在diff的时候不会看到*.xcodeproj相关的信息，git会把它当作二进制文件来处理。

我推荐使用第二种方法。

这样有99%的情况，可以解决merge的问题。剩下的1%。只能用土方法了。取出线上没问题的文件，然手工添加。

当然了，也可以人肉merge，今天我用vim的%查找匹配的`{`和`}`。可以比较快速的定位，从最后一个`}`开始查找。


[sof]: https://stackoverflow.com/questions/2615378/how-to-use-git-properly-with-xcode?answertab=active#tab-top
[blockquoteTom]: https://stackoverflow.com/questions/2615378/how-to-use-git-properly-with-xcode?answertab=active#comment2626908_2615378