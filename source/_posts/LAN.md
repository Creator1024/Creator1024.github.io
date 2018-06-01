---
title: LAN
toc: true
date: 2018-05-29 22:54:48
tags: reviewDay1
categories:
- 数据通信
- 基本概念

---

#### 1.LAN基础
* 局域网指的是有限区域内相对距离较短的计算机和其他组件的网络，主要包含了物理层和数据链路层，一般我们接触到的局域网都是以太网组成的。

* 以太网发展历程
```
1973年：      以太网（Xerox公司）

1980年：      DIX 标准版（以太网II）【Digital Equipment Corp，Intel，Xerox】  

1983年：      10 BASE-5   -->10Mb/s 粗同轴电缆以太网(IEEE802.3)  

1985年：      10 BASE-2   -->10Mb/s 细同轴电缆以太网(IEEE802.3a)  

1990年：      10 BASE-T   -->10Mb/s双绞线(TP)以太网（IEEE802.3i）【twisted-pair】  

1993年：      10 BASE-F   -->10Mb/s光纤以太网（IEEE802.3j）【fiber】  

1995年：      100 BASE-xx  --> 快速以太网：100Mb/s双绞线和光纤以太网（IEEE802.3u）  

1998年：      1000 BASE-X -->千兆光纤以太网（IEEE802.3z）  

1999年：      1000 BASE-T -->千兆双绞线以太网（IEEE802.3ab）      

2002年 :       10G BASE-xx  -->10千兆光纤以太网（IEEE 802.3ae）     

2006年 :       10G BASE-T  -->10千兆双绞线以太网(IEEE 802.3an)   
```

* 以太网中的流量主要包括：
    * 数据流量（用户发的流量，以太网）
    * 控制流量（交换机之间交互的流量，IEEE802.3)

#### 2.MAC地址

* MAC地址组成（烧录地址）
    * 24位（3字节）OUI【组织唯一标识符】+24位（3字节）厂商分配的网卡，接口。
    * Cisco设备：0000.0000.0000
    * Linux: 00:00:00:00:00:00
    * Windows: 00-00-00-00-00-00


* [查询设备mac地址信息](https://www.macvendorlookup.com/)
#### 3.帧格式

前导码和帧开始符无法在包嗅探程序中显示。这些信息会在OSI第1层被网卡处理掉，而不会传入嗅探程序采集数据的OSI第2层。也存在OSI物理层的嗅探工具以显示这些前导码和帧开始符，但这些设备大多昂贵，多用于检测硬件相关的故障。
##### 以太网帧格式



![](https://upload-images.jianshu.io/upload_images/12329802-489aa87915cf0214.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Preamble（前导码）：用于帧同步
* Type：标识上层协议
    * 0x0800   ipv4
    * 0x0806   arp
    * 0x8100   IEEE802.1Q(dot1q)
    * 0x86DD  ipv6
    * 0x8864   PPPoE 
    * 0x8847   MPLSLabel 


##### IEEE802.3帧格式


![](https://upload-images.jianshu.io/upload_images/12329802-eb829973b19a75ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* Preamble：同以太网帧
* SOF（帧首定界符）：用于标识一个帧的开始
* dot1q的标签插在Source和Type之间



