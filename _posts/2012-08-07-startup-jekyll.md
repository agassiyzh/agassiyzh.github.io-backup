---
layout: post
title: "Startup jekyll"
date: 2012-08-07 16:52
comments: true
categories: 
---
之前用的的是[Octopress][]的。其实这个八爪鱼也挺好的。比Jekyll用起来方便多了。各种好用的插件默认就有，而且默认的主题也相当好看。对不同屏幕尺寸也做了很好的适配。

至于换成Jekyll纯粹是因为最近心血来潮，想自己写写页面。还有就是玩玩Bootstrap，haml/sass，compass这些东西。玩了一圈这些工具确实挺好用的。

compass的sprite功能超级好用的。之前我们做网页为了减少一个页面的请求数量，常用的做法是吧很多小图片拼接成一张大图。然后来去各种位置。这个工作是相当恶心的如果是纯手工的话。

[Compass][]可以直接import一个image的文件夹。然后在compile的时候自动生成一张拼接好的大图。而且在生成的css文件中已经有了个小图的位置信息。在sass文件中只要@extend这个小图的文件名就可以方便取用了。[详细介绍可以看看compass的sprite功能介绍页面][1]。

再来说说[BootStrap][]，twitter官方的版本是支持Less的。但是在github上有一个项目把它做成了支持sass的本版本。而且和compass配合的也很好。它是[bootstrap-sass][]。

顺便学了一下Ruby，rake神马的。

[Octopress]: https://octopress.org
[1]: https://compass-style.org/help/tutorials/spriting/
[bootstrap-sass]: https://github.com/thomas-mcdonald/bootstrap-sass/
[BootStrap]: https://twitter.github.com/bootstrap/
[Compass]: https://compass-style.org