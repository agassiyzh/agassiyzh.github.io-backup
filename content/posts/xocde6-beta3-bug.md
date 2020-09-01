---
title: "Xcode6 beta3(6A254o) all-product-headers.yaml 错误"
date: 2014-07-10T16:16:00+08:00
---

Apple最近放出了iOS8 beta3，Mac OSX yosemite DP3, Xcode6 beta3(版本号是6A254o)更新。在用xcode的时候会出现一个奇怪的错误导致编译不能通过。

```
fatal error: invalid virtual filesystem overlay file '/[Long-path-goes-here]/all-product-headers.yaml'
1 error generated.
```

![xcode6 beta3 bug](/images/xcode-beta3-bug.png)

<!--more-->

通过问Google在[Stackoverflow: Xcode 6 beta 3: invalid virtual filesystem overlay file][stackoverflow]找到了解决办法。

原因是Xcode在编译生成`all-product-headers.yaml`的时候格式有错误，最后两个括号没有括回去。

### 解决方法：

1. 找到`/[Long-path-goes-here]/all-product-headers.yaml`这个文件
2. 把最后的方括号和大括号补全
3. 保持并设置这个文件为只读（每次编译的时候这个文件会被重新生成）

补全后的文件像这样：

```json
{
  'version': 0,
  'case-sensitive': 'false',
  'roots': [
  ]
}
```

目前没有更好的解决办法，只能等Apple 下次更新了。不知道在beta4之前会不会发布一个紧急修正版。

[stackoverflow]: https://stackoverflow.com/questions/24622650/xcode-6-beta-3-invalid-virtual-filesystem-overlay-file