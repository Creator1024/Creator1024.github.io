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

![](https://upload-images.jianshu.io/upload_images/12329802-70170e3bb7c23a77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 发展历程
    * EthernetV1（1980），Xerox和DEC、Intel公司共同提出的标准
    * EthernetV2（1982），目前大部分的以太网帧都是使用这个类型



* Type：标识上层协议（大于**0x0600**）
    * 0x0800   ipv4
    * 0x0806   arp
    * 0x8100   IEEE802.1Q(dot1q)
    * 0x86DD  ipv6
    * 0x8864   PPPoE 
    * 0x8847   MPLSLabel 
* 以太网帧的长度区间为64-1518字节（6+6+2+4+[46/1500]）

##### IEEE802.3帧格式
 ![](https://upload-images.jianshu.io/upload_images/12329802-bbb979de7554dbe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 802.3帧的长度区间也是64-1518字节
* 发展历程
    * RAW802.3（Novell，1983）：
     只支持IPX/SPX，将EthernetV2中的Tpye改为Length，Novell私有  

    * IEEE802.3/802.2 LLC（1985）：
     IEEE正式的802.3标准，由EthernetV2发展而来，将Tpye改为Length，并且只能封装一种上层服务，即LLC，LLC子层中（通过DSAP和SSAP）区分更上层的服务。
     
        * 交换机发送BPDU使用802.3/LLC模式（DSAP和SSAP为0x42）
        ![](https://upload-images.jianshu.io/upload_images/12329802-161b3838ddda2205.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
        
        * 这里使用的Length字段，表示上层数据长度

    * IEEE802.3/802.2 SNAP（1985）：  
     为了在802 LLC上支持更多的上层协议同时更好的支持IP协议而发布，扩展了LLC属性，增加了一个2Byte的协议类型域（PID）和3Byte的厂商标识符（OUI）。

        * VTP使用的是802.3/SNAP模式：
        ![](https://upload-images.jianshu.io/upload_images/12329802-dd56998296701e36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
        
        * 这里使用的是Tpye字段，表示上层所使用的协议
        
    * 当Tpye/Length字段小于**1500,十六进制0x05DC**时，对应802.3/LLC帧，当其大于**1536,十六进制0x0600**时，对应的是802.3/SNAP帧（与EthernetV2兼容）
  
* LLC中DSAP和SSAP取值不同的时候，代表不同的802.3类型帧
    * 当DSAP和SSAP都取特定值**0xff**时，802.3帧就变成了Netware-ETHERNET帧，用来承载NetWare（Novell的网络操作系统）类型的数据。
    * 当DSAP和SSAP都取特定值**0xaa**时，802.3帧就变成了ETHERNET_SNAP帧。ETHERNET_SNAP帧可   以用于传输多种协议。（如上图VTP使用的帧类型）
        * DTP——0x2004
        * VTP——0x2003
        * CDP——0x2000
    * DSAP和SSAP其他的取值均为IEEE802.3/LLC帧。（如BPDU使用的帧类型）
       














