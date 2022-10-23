---
layout: post
title:  "How to Understand SGMI"
---

## 什么是SGMII？

先说什么是GMII/MII。 MII是ethernet协议里面MAC层和PHY层之间的接口标准。MII是4bits的数据位宽，支持10/100M的数据传输。GMII前面G表示Gigabit，代表支持1000M的传输速率。需要说明的是MII是GMII的子集，也即是说支持GMII标准的设备，同时支持10/100/1000M三种模式。
SGMII前面的S代表Serial，即串行的意思。前面说了MII的数据位宽是4bits，GMII是8bits，SGMII则是1bit。
需要澄清的是SGMII是否只支持1G速率？答案是：错误！同时支持三种速率。具体速率是通过自协商来决定的，如果某个厂商的设备只做了1种速率支持，其实是实现了协议的一个子集，在某种场景下也是可以工作，而不是说协议只规定了一种速率模式。

## 为什么需要用SGMII？

SGMII协议是CISCO公司提出来的，可以减少芯片间互联的管脚。传统的GMII前面说了是8bits数据线，此外还需要时钟，和一些控制线，双向加起来要20根线左右。而SGMII接口是1根数据线加1根时钟线，双向共4根。如果去掉时钟线（采用CDR），那么2根线就可以实现互联了。

## SGMII接口如何与PHY芯片互联？
![SGMII Connect](/assets/sgmii_connect.png)

SGMII的时序与电气特性也是有规定的，时序上采用类似DDR的接口，电平采用LVDS标准。
![SGMII IO](/assets/sgmii_io.png)
协议里规定了输出信号需要提供一个半速率、90度相移的时钟信号。同时也允许接收端采用CDR恢复时钟的方式。

既然已经串行化了是不是不用接PHY芯片了？答案是否定的。因为常用ethernet介质为双绞线。而802.3协议里的物理层定义的信号为PAM5。而PCS输出的信号为NRZ信号。当然如果用sgmii实现两个芯片的mac层短距互联也是可以的，这就超出了802.3协议的定义了。


## SGMII如何实施？
SGMII本质上并没有对以太网协议的分层做改动，还是MAC层，PCS层和PMA层。原来GMII模式下，MAC层一般做在SOC侧，PHY层包括PCS+PMA做在另一个单独的芯片上。而SGMII的实施是将PCS层也同时放在了原来的MAC侧。这样SOC芯片和PHY芯片各有一个PCS层。
对于SOC发送来说，数据包有MAC层过来，经过tx 的pcs，从SGMII接口发送出去。在PHY芯片上，有一个rx的pcs先将SGMII的信号解出GMII信号，然后再经过传统的PHY层处理发送到介质上。对于SOC接收来说，则反过来。

## SGMII如何自协商？
SGMII的自协商从功能角度来说采用1G以太（802.3z）的自协商功能。即pcs和phy之间传递参数。但发送的内容和802.3z协议里定义的参数格式不同。
![SGMII AN Table](/assets/sgmii_antab.png)
从上表可以看到SGMII的自协商参数内容。流程上是PHY将配置发给PCS，PCS发送确认信息。值得注意的是此处的自协商是指802.3中第37章节里定义的PCS自协商，是不包括链路信息的。

>以上信息解读来自于Serial-GMII Specification version 1.8。
