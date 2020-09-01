---
title: Mac开机自动启动shadowsocks
date: 2013-02-28T14:23:29+08:00
tags: [shadowsocks]
---

最近开始用[shadowsocks]来和世界人民保持联系了。这个工具非常之好。以至于我非常写给作者[买杯啤酒][v2ex]什么的。但是都被[clowwindy]老师婉言谢绝了。

----

[shadowsocks]可以用[ohdarling88]老师的GUI程序[GoAgentX]。配置启动都相当方便。

[GoAgentX]中用到的是[Python]版本的[shadowsocks],我想用[nodejs]版本的。还有就是我觉得[shadowsocks]这样的神工具应该是在开机到关机一直在后台运行的。虽然[GoAgentX]也可以开机启动，但是每次启动要允许防火墙输入密码什么的。

<!--more-->

于是做了一个开机启动的plist：


{{< gist agassiyzh 5020877 >}}

这个是[nodejs]版本的，如果是用[python]版本的可以稍微改改。

把脚本放在`~/Library/LaunchAgents`就可以了。


[v2ex]: https://www.v2ex.com/t/61258?r=agassi_yzh
[clowwindy]: https://twitter.com/clowwindy
[shadowsocks]: https://www.shadowsocks.com
[ohdarling88]: https://twitter.com/ohdarling88
[goagentx]: https://github.com/ohdarling/GoAgentX
[Python]: https://github.com/clowwindy/shadowsocks
[NodeJS]: https://github.com/clowwindy/shadowsocks-nodejs