---
title: IP
toc: true
date: 2018-06-02 15:11:59
tags:
categories:
- 数据通信
- 协议分析
---

##### 1.IP头部组成 
![](http://upload-images.jianshu.io/upload_images/12329802-3a519c9a03acb206?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 版本：IP协议的版本
    * 0100——IPV4
    * 0110——IPV6 
* 首部长度：ip报文的首部长度，20-60字节
* 服务类型：

![](http://upload-images.jianshu.io/upload_images/12329802-33330c11c338b607?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

       
```python 
优先级用来区别优先级别不同的IP报文（PPP）

D表示要求有更低的时延

T表示要求有更高的吞吐量

R表示要求有更高的可靠性

C标识要求有更小的费用
```   
      

* 总长度：报文的长度（最大65535字节）

* 标识：数据报长度超过MTU（最大传输单元），就要进行分片，这个标识字段的值被复制到所有数据包分片的标识字段中，使得这些分片在达到最终目的地的时候可以按照标识字段的内容重新组成原来的完整数据报。

* 标志：最低位MF,MF=1时，表示后面还有分片，中间位为DF,DF=1时，表示不能分片。

* 片偏移：本分片在原先数据报文中相对于首位的偏移位。

* 生存时间：表示数据报在网络中存活的时间，所允许通过的路由器最大数量。每通过一个路由器，则TTL值减少1，当其值为0时，路由器就可以把该数据包丢弃。

* 协议：标识上层（传输层或者是其他封装）所使用的协议。常用的协议号：
    * ICMP 1 
    * IGMP 2 
    * TCP 6 
    * UDP 17 
    * IGRP 88 
    * OSPF 89
    * （IPv4-in-IPv4） 4

 
* 首部校验和：对IP头部做正确性检测，不包含数据部分校验方法：在发送端，将IP数据报首部划分为多个16位的二进制序列，并将首部校验和字段置为0，用反码运算将所有16位序列对位相加后，将得到多的和的反码写入首部校验和字段。接收端接收到数据报后，将数据报首部的所有字段组织成多个16位的二进制序列，再使用反码运算相加一次，将得到的结果取反。如果结果为0代表没出错，否则出错。——具体算法实现：（待补充）

* 源地址和目的地址：标识了IP包的起源和目的地址。除非使用NAT，否则传输过程这两个值不变

* 可选项：由起源设备根据需要添加改写。

* 填充：IP报头长度部分的单位为32bit，所以其长度必须为32bit的整数倍，若没达到，则会填充若干个0，达到32比特。


##### 2.IP分片 

![](https://upload-images.jianshu.io/upload_images/12329802-0f810241fc31ccc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


R1#ping 23.1.1.3 size 2000，在R2的右侧接口分析流量，可以看到数据包被分片传输

![](https://upload-images.jianshu.io/upload_images/12329802-68ee949e148f13e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


![](https://upload-images.jianshu.io/upload_images/12329802-8a2d8aa68c929b5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
第一个数据帧——
* 总长度为1518字节：
    * 以太网帧头14字节
        * SrcMAC，6字节
        * detMAC，6字节
        * Type，2字节
    * IP头部20字节
    * 数据1480字节

* IP头部Flags-MF置为1，表示还有更多的分片

![](https://upload-images.jianshu.io/upload_images/12329802-7d19d764eedb379e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第二个数据帧——
* 总长度为534字节：
    * 以太网帧头14字节
    * IP头部20字节
    * 数据500字节
* Fragment offset为1480，即表示此分组中的数据是从第1481个字节开始的，之前的分组已经传出了1480字节的数据（不包括IP头部）


两个分组的数据长度（不包括IP头）总和为1480+500=1980，第二个数据包内显示数据长度为1972字节。  
所以R1#ping 23.1.1.3 size 2000所发送数据具体的内容应为：
* IP头部20字节
* ICMP头部8字节
* ICMP数据1972字节

再来看看分片后的数据包大小（包括IP头）：  
* 第一个数据包1500
* 第二个数据包520
* 二者总和2020字节，比原来多了20字节，即多出了一个没有填充的IP头部长度。





