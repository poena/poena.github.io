---
layout: post
title:  "How to Understand Backplane Ethernet"
---


以太网协议规范十分的繁多。按照传输介质来分，主要有三种场景：基于双绞线的、基于光纤的、基于背板的。本文主要介绍背板相关的以太网协议。

### 协议概述：
以太网协议按照feature分了很多的章节（即：clause）。其中介绍背板以太网（Backplane Ethernet）的是Clause 69。
背板以太网是一系列的协议，速度等级涵盖了：1000 Mb/s, 10 Gb/s, 25 Gb/s, 40 Gb/s, or 100 Gb/s。
对应的物理层标准如下：

 - 1000BASE-KX for 1 Gb/s operation over a single lane
 - 10GBASE-KX4 for 10 Gb/s operation over four lanes
 - 10GBASE-KR for 10 Gb/s  operation over a single lane
 - 25GBASE-KR and 25GBASE-KR-S for 25Gb/s operation over a single lane
 - 40GBASE-KR4 for 40 Gb/s operation over four lanes
 - 100GBASE-KR4 and 100GBASE-KP4 for 100 Gb/s operation over four lanes

### 命名规则
我们可以观察到协议的命名规则，前面的数字代表速率，最后的数字代表几条lane，如果是1条lane就没有数字。中间的有两类：KX和KR，分别代表8b10b pcs和64/66 pcs。
背板以太网是在基于光传输的以太网基础上衍生出来的。如果是10G光口对应的命名为10GBASE-X和10GBASE-R。背板的命名是在此基础上增加了K。


### OSI结构
![Ethernet OSI Architecture](/assets/backplane_osi.png)

既然说背板是在光口基础上的扩展，那么跟基于光口的以太网有哪些不同呢？从OSI模型来看，PCS层多了FEC和AN子层。其中FEC是可选功能，而AN则是必须的。AN是指自协商，用于协商链路两端的能力和速率。此外10G以上的背板以太网的PMD层还要支持链路训练。

### 相关定义
背板相关的feature在clause 70，71，72里。
![Physical Layer Define](/assets/backplane_pl.png)
以10G-KR为例，其中定义了哪些feature是可选功能，哪些feature是必须的功能。这些feature对应的章节号也在上表里写出来了。

其中比较重要的是clause 73，写明了自协商的规范。

### 增补
在2019年增补的以太网协议中，定义了2.5G和5G的背板协议，称为：ieee802.3cb。

### MAC层接口及速率
PCS和MAC之间的接口是GMII和XGMII接口。其中1G及以下速率采用GMII；1G～10G之间采用XGMII接口。
![Speed up for 2.5G](/assets/backplane_speed.png)
2.5G可以是1G的以太超频2.5倍实现。


### 波特率
上面的以太网速率是指基带速率，而serdes侧的波特率是在此基础上再乘以编码效率。比如6466编码需要乘以66/64；如果是8b10b编码需要乘以10/8。因此10G-KR的波特率是10.3125G；1000-KX的波特率是1.25G；2.5G-KX的波特率是3.125G。

多lane的以太网，比如100G可以是10x10G，或者4x25G。serdes的线速率为10.3125G和25.78125G。

### 协议的兼容性
背板以太网是兼容光口以太网的。比如10GBASE-KR兼容10GBASE-R。协议层上背板协议增加了FEC和自协商等功能。这些功能是可以关闭的。这样底层编解码处理上就没有什么区别了。
1000BASE-X与SGMII在线速率上是相同的，区别主要是自协商方式不同。SGMII采用CL37定义的自协商，速率可以向下兼容10/100M而1000BASE-X不支持更低速率。值得注意的是，SGMII即使协商为低速模式，serdes侧的速率仍然为1.25G。
