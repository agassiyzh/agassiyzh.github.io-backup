---
title: "ModBus 空调组件及中央空调接入 Home Assistant 细节"
date: "2018-10-29T19:24:00+08:00"
---

<div style="width: 100%;display: flex;display: -webkit-flex;">
    <!-- <video style="width:480px;margin: 0 auto;display:block;" src="/images/HA-climate-modbus/siri.mp4" controls="controls"/> -->
    <div style="width:66%;">
        <video style="width:95%;" src="/images/HA-climate-modbus/siri.mp4" controls="controls">
        </video>
    </div>
    <div style="width:32%;">
        <img src="/images/HA-climate-modbus/HA1.png"/>
        <img src="/images/HA-climate-modbus/HA2.png"/>
    </div>
</div>


玩Home Assistant有一段日子了。一直想把家里的中央空调接入进去。无意间Google到`Yonsm`老师的blog好像看到了一线希望：[《ModBus 空调组件及中央空调接入 Home Assistant 简述》][1]。

于是按照文章和论坛中讨论的指引购入了相关的模块。由于第一次玩modbus这个工业互联协议，中间也走了不少弯路。这里对[《ModBus 空调组件及中央空调接入 Home Assistant 简述》][1]没有提及的细节做一个补充.

开始之前首先要在心理上做好打持久战的准备😱️

<!--more-->


## 需要材料

1. modbus空调网关
2. 网关电源
3. 万用表（测电源极性）
4. modbus 转TCP串口服务器

![tools](/images/HA-climate-modbus/tools.jpeg)

## 12V变压器正负极

{{< figure src="/images/HA-climate-modbus/wechat1.png" class="floatright" width="100px" >}}

空调智能网关的电源接口是一对正负极的接线端子，电压12V-24V。发货过来没有配电源。我把家里闲置的交换机电源剪掉DC接头，剥出两根正负极电源线插进去。问题是直流电源，我并不知道哪一根是正极哪一根是负极。线的胶皮上有一根是黑白相间的，有一根是全黑的。我想应该有一个工业标准来规定线的正负极线的颜色。Google之后众说纷纭。问了卖家技术指导（见图）：

保险起见还是在马云家买了个万用表测了电源正负极，接通连上。


## 如何获取modbus协议地址

按照`简介`中提供的配置方案，控制空调的各个操作都是通过对modbus不同的协议地址的读写访问进行的。所以获取modbus的协议地址是成功的关键。

卖家告诉我需要用到[modbuspoll][modbuspoll]。

## 空调网关指示灯

商品页面上介绍，空调网关正常状态应该是`绿灯闪烁`。而我拿到的机器一直是`红灯闪烁`。

![modbus light](/images/HA-climate-modbus/modbus_light.png)

![](/images/HA-climate-modbus/wechat2.png){:style="width:80px;float: right;margin-right: 7px;margin-top: 7px; margin-left: 15px;"}

问了店家后确认是批次问题，有些批次是红灯正常的。。。emmmm只能抱着将信将疑的态度继续调试了。



## 空调智能网关调试

为了确定我收到的网关是否有故障，按照技术指导的提醒可以直接把网关连到电脑的串口。由于我身边的电脑都是Mac，只能又在马老板家买了USB转RS 232 模块，特地选了带串口RS 232转485的。

![](/images/HA-climate-modbus/adapter_tb.png){:style="width:60px;float: left;margin-right: 7px;margin-top: 7px; margin-left: 15px;"}

按照说明连好线路，设置好串口和modbuspoll，在可以在5xxx的地址段看到数据表示空调网关可以正常工作。

## 串口服务器配置

有人w610支持内部modbus RTU转modbus IP。我一开始理所当然的认为应该选择`modebus TCP <=> modbus RTU`模式，然后在modbuspoll中也设置成了这个模式。几经波折后才想明白：在串口服务器上选择这个模式后就已经做了一次转换，在modbuspoll中收到的数据格式应该是`modbus TCP`。

我最后的解决方案是在串口服务器上数据传输模式设置成`透明传输模式`，在调试时modbuspoll中设置成`modbus TCP <=> modbus RTU`。

其他的设置相对简单，我空调机位边没有网线。所以串口服务器配置成连接家里的无线网络（STA模式）。

串口相关设置可以参考截图。

<div style="width: 100%;display: flex;display: -webkit-flex;">
<div style="width: 40%" ><img src="/images/HA-climate-modbus/modbus_server_config_1.png" /> </div>
<div style="width: 48%" ><img src="/images/HA-climate-modbus/modbus_server_config_2.png" /> </div>
</div>


## modbuspoll配置

前面有提到modbuspoll 连接设置里面模式选择`modbus TCP <=> modbus RTU`，填写好串口服务器IP和端口地址就可以了。

modbus协议地址可以通过在modbuspoll中设置`Read/Write Definition`来获取。

具体做法：新建窗口 ➡️️ 右键菜单`Read/Write Definition` ➡️️  `Function`选不同的地址段位，`Quantity`可以输入最大值 125。

<div style="width: 100%;display: flex;display: -webkit-flex; flex-wrap: wrap;">
    <div style="width: 100%"><img src="/images/HA-climate-modbus/modbuspoll1.jpeg"></div>
    <div style="width: 50%"><img src="/images/HA-climate-modbus/modbuspoll2.jpeg"></div>
    <div  style="width: 50%"><img src="/images/HA-climate-modbus/modbuspoll3.jpeg"></div>
</div>

我这里有一个快捷方式，修改地址就能用。

[modbuspoll 快捷方式](/files/Modbus-Poll测试数据.rar)


## 信号线

![](/images/HA-climate-modbus/line_tb.png){:style="width:100px;float: right;margin-right: 7px;margin-top: 7px; margin-left: 15px;"}

一开始用DC电源上剪下来的线作为信号线，但是屡次失败后怀疑是线材的问题。问了店家技术指导说短距离应该没问题。保险起见还是在马云家买了标准的信号线材，跟空调公司安装的一样。分别买了0.5平和1平的，实际使用发现0.5平正好。

## 空调线路

<div style="width: 100%;display: flex;display: -webkit-flex; flex-wrap: wrap;">
    <div style="width: 50%"><img src="/images/HA-climate-modbus/circuit_diagram.jpeg"></div>
    <div  style="width: 50%"><img src="/images/HA-climate-modbus/interface.jpeg"></div>
</div>

Yonsm老师说接空调线路是最麻烦的。不过他也在文中提到了只要`接空调的F1、F2（空调的第三、第四接口）端子到空调网关U3的A、B接口就可以了`。

我的海信荣耀系列跟大金不太一样。拆下检修口保护罩，背后有一副电路图。看到`A、B`接口后我兴奋地认为这就是我要找的接口。调试无果后，第二次拆下保护罩发现旁边有`switch`的字样。原来这是这台内机的控制面板端口。按照modbus的原理，我大概猜测，这个端口因该只会有这台内机的数据信号。正确的端口因该是旁边的两个`通讯/Transmission`端子。

<blockquote class="blockquote-center" cite="https://bbs.hassbian.com/forum.php?mod=redirect&goto=findpost&ptid=3581&pid=89367">一般的情况（1外机+多内机）是内外机都可以。如果是那种多外机的情况，要接在外面，但这个一般家庭没有多外机的重要空调吧。
<br /><br />
<strong>Yonsm</strong>
</blockquote>

## slave 地址

modbus默认slave地址是1，我家海信的地址是1不需要修改（据说大金不是1）。`HomeAssistant`需要改地址可以添加参数`slave: x`

例如：

```json
temperature: {registers: [3,7,11], register_type: input, scale: 0.1, slave: 3}
```

没有验证，来源：[帖子80楼](https://bbs.hassbian.com/forum.php?mod=redirect&goto=findpost&ptid=3581&pid=125911)


## 调试顺序

如果你手上正好有串口转modbus 485的模块，可以先连上调试一下。看一下modbuspoll 中5xxx段有没有数据。

否则可以先连接好串口服务器和空调智能网关，配置好串口服务器后。通过wifi在modbus中取到5xxx段的数据（一般来说两个硬件不会有问题）。然后把空调连上空调网关U3端口，通过wifi取到对应的协议地址。






[1]: https://yonsm.github.io/modbus/
[modbuspoll]: https://www.modbustools.com/modbus_poll.html
