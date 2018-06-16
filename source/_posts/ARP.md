---
title: ARP
toc: true
date: 2018-06-01 20:42:46
tags:
- ARP
categories:
- 数据通信
- 协议分析
---




##### 1.作用
  

  

* 为什么主机需要有各自的ipv4地址和mac地址才能通信呢？  
  ip属于网络层，mac地址属于数据链路层。通过沿ip传输的路径是逻辑路径，由路由设备根据路由表进行转发，而一条逻辑路径包括多个数据链路。沿独立的数据链路传播的时候，就需要用到一种数据链路标识，以太网中的mac地址就起到这样的作用。

* 通常，主机刚开始发送数据的时候知道对方的ip地址，而不知道对方的mac地址。这时候就需要用到arp协议获悉到目的主机的mac地址，成功获悉到后便能正常封装数据包，并发送出去。  
##### 2.报文结构
![](https://upload-images.jianshu.io/upload_images/12329802-407deff2aa2a3930.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 3.代理ARP

* 代理arp被路由器作为向主机表明自身可用的一种手段：  
当开启代理ARP的路由器接口收到一个arp请求后，会查看目的ip，是否能根据本地的路由表到达，若可到达，则将自身收到arp请求的接口的mac地址回复给请求节点，告诉其去往目的网络可往这里发送。若不可达，则无视。

###### 3.1静态路由与代理arp

![](https://upload-images.jianshu.io/upload_images/12329802-f9d57fe0efef8b2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

R2的g0/2接口开启代理arp功能并有返回192.168.1.0/24的静态路由

当R1使用静态路由    
    ip route 10.1.1.0 255.255.255.0 g0/0  
pc0分别ping其他两台主机：

![](https://upload-images.jianshu.io/upload_images/12329802-a25c541bf21063df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


R1的arp表：  
![](https://upload-images.jianshu.io/upload_images/12329802-58451b73113baf84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

R2的g0/2口接口信息：  
![](https://upload-images.jianshu.io/upload_images/12329802-bb252b20c084fa00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 分析：  
PC0 ping PC1，R1转发数据包的时候，自身arp表中没有10.1.1.1地址对应的mac地址，于是发送一条arp广播请求，R2的g0/2接口收到arp请求，由于开启了代理arp，并且拥有到达目的网络的ip，所以将自己g0/2的mac地址回复给R1（请求者）。当PC0 ping PC2的时候，依然重复此步骤。  
故R1的arp表中，10.1.1.1与10.1.1.2所对应的mac地址都是R2的g0/2口的mac地址。（如果arp表中多个ip地址映射到单一的mac地址往往是开启了代理arp）   
使用跟出接口的静态路由，转发每一个报文都要发送arp查询，在MA网络中，如果ARP报文大量发送，易造成广播风暴。  

* 若将R1的路由改为  
    ip route 10.1.1.0 255.255.255.0 12.1.1.2  

![](https://upload-images.jianshu.io/upload_images/12329802-f3d6dbbb9bf6761e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 

![](https://upload-images.jianshu.io/upload_images/12329802-e56cf9147db12636.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


R1转发数据包之前，会先发送arp请求查询下一跳（12.1.1.2）的mac地址，之后这条路径就无需每次都发送arp查询。  

##### 4.无故（Gratuitous） ARP  
主机偶尔会以自己的IPv4地址作为目标地址发送ARP请求，此为无故ARP，或称为免费ARP。其主要有两种作用：

* 用来检测网络中是否有ipv4地址冲突
* 用于通告一个新的数据链路标识符  
 
![](https://upload-images.jianshu.io/upload_images/12329802-a778ae1a08412f9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将R1的ip地址先配置为192.168.1.1/24，再配置成192.168.1.3/24。

![](https://upload-images.jianshu.io/upload_images/12329802-c9c0e8483de022c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过抓包分析可知：gratuitous arp是一个replay arp，操作码值为2，其发送者和接受者皆为自身。  路由器每次更改接口ip地址的时候，都会发送一个gratuitous arp来避免ip冲突。另外，windows和linux启动后都会发送无故arp。 

  
若将R2的f0/0的ip地址也改为192.168.1.3,则会出现地址冲突错误。  
![](https://upload-images.jianshu.io/upload_images/12329802-f6fc40fc53ab72f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/12329802-29befa12657b7743.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

如果不解决这个问题，路由器会继续一直发送gratuitous arp......


##### 5.反向ARP（RARP）

RARP作用是将数据链路标识符映射为ipv4地址，早期的无盘工作站中会使用到。不过RARP在很大程度上被DHCP和BOOTP的扩展协议所替代，他们能提供更多的信息。 
