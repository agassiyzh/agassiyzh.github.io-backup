---
title: "vim插件推荐：Syntastic"
date: 2012-08-30T14:50:00+08:00
categories: vim
---

早上看到主席大人[@TualatriX][tx]老师推荐了[Syntastic][syntastic]这个vim插件。

> [Syntastic][syntastic]这个Vim插件真是好东西阿：https://github.com/scrooloose/syntastic 可以每次在保存文件时检查语法和代码。比如Python中定义了一个变量但没有用，就会高亮警告。
> 
> 	**[@TualatriX][tx]** via Twitter

于是用[Vandle][vandle]装上。开始无法正常工作，不管是python还是PHP。仔细看了一下doc。

不同的语言需要单独配置，需要看看```syntax_checkers```目录下面相应的语言的配置。python需要安装```["flake8", "pyflakes", "pylint"]```其中一个。

<!--more-->

我装了flake8:

	$sudo pip install flake8

PHP的话目前（2012－08-30）支持不支持PHP-5.3和PHP-5.4的。我的OS X 10.8.1上php的版本是5.3.13。我去看了下一github上的Pull Reuqests。[Issues #320][#320]的一个提交[a26d3ae][a26d3ae]解决了这个问题，目前还没有合并进入master。我们可以自己来合并(这也是vundle来管理vim插件的一个好处)：

{% highlight bash %}
$cd <syntastic plugin path>
$git remote add rbrown git://github.com/rbrown/syntastic.git
$git fetch rbrown
$git merge rbrown/fix_php_53_and_54
{% endhighlight %}

好了，看接图：
瞧第二行的红叉！
![screen shot][screen shot]

[tx]: https://twitter.com/tualatrix 
[syntastic]: https://https://github.com/scrooloose/syntastic
[vandle]: https://github.com/gmarik/vundle
[#320]: https://github.com/scrooloose/syntastic/pull/320
[a26d3ae]: https://github.com/rbrown/syntastic/commit/a26d3aef58a26f2b47ef994f93ce2cf0b2cd06aa
[screen shot]:https://pic.yupoo.com/agassi/CeiZsZ5U/11aOIG.png