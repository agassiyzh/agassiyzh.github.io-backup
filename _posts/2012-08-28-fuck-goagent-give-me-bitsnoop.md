---
layout: post
title: "翻越Goagent，还我bitsnoop"
date: 2012-08-28 18:07
comments: true
categories: 

---

说起```那事儿```很多志士仁人选择了```GoAgent```,我之前都是用我的VPS连ssh来搞的。在Mac上面用autossh+launchctl+pac的方案基本上也可以无视```那事儿```了，但是苦于VPS在美帝，速度总是上不去。

今天蛋疼搞了一下GoAgent，太神啦。好快！用的是[@ohdarling88][ohdarling88]老爷的牛屄GUI客户端[GoAgentx][GoAgentx]，带部署GAE服务功能，还支持ssh什么的。反正你们去看看就知道了。

<!-- more -->

![GUI][GUI]

搞定以后上了一下测试了一下[bitsnoop][bitsnoop],竟然跳转到127.0.0.1了。各种调试无果，心想GoAgent不会有这个诡异的情况吧。结果关了GoAgent就好了。Google了一下，很多人碰到了这个问题。有人说是google“封”了bitsnoop。

Privoxy和2.0.2版的GoAgent配合也有问题（为了解决Dropbox的同步）。

于是我保留了两种```干那事儿```的方案。原来的SSH依然用autossh保持连接，让[bitsnoop][bitsnoop]和priboxy分流走这个通道。

开始我在GoAgentX中设置选择```不修改系统代理设置```然后在系统中用自己修改的pac。除了bitsnoop走ssh，其他需要代理的走GoAgent。

这样的坏处是修改配置比较麻烦，每次都要打开编辑器什么的，没有在GoAgentX中设置来的方便。

后来看了下GoAgentX，看到有个pactemple.pac的文件，用base64编码过的。解码后做了一点自己的修改：

{% highlight javascript %}
function FindProxyForURL(url, host) {
  var PROXY = "PROXY 127.0.0.1:8087";
  var DEFAULT = "DIRECT";
  var SOCKS_PROXY = 'SOCKS5 127.0.0.1:7070; SOCKS 127.0.0.1:7070; DIRECT';  //自己的代理
  
  function socksProxyDomains (url, host) {
	if (false
		|| shExpMatch(host, "*bitsnoop.com")) {
		return true;
	}

	return false;
  }
  
  if (socksProxyDomains(url, host)) {
	return SOCKS_PROXY;
  }
  
  …
}
{% endhighlight %}

然后在base64一下，替换app里面的这个文件就可以了。

然后我打算看看GoAgentX的源码，加个第二代理的设置进去。

[ohdarling88]: https://twitter.com/ohdarling88
[GoAgentx]: https://github.com/ohdarling/GoAgentX
[GUI]: https://raw.github.com/ohdarling/GoAgentX/master/Screenshot.png
[bitsnoop]: https://bitsnoop.com