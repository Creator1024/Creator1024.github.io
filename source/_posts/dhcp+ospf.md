---
title: dhcp+ospf
toc: true
date: 2018-05-24 21:31:59
tags: 
- huawei
- dhcp
- ospf 
categories:
- 数据通信
- R&S配置
- 华为
---
#### HUAWEI DHCP+OSPF Test

    R1:
    dhcp enable //全局下先开启DHCP功能
    ip pool network1
     gateway-list 192.168.1.1
     network 192.168.1.0 mask 255.255.255.0
     excluded-ip-address 192.168.1.2 //排除地址 
     dns-list 192.168.1.1
    
    interface Ethernet0/0/0
     ip address 192.168.1.1 255.255.255.0
     dhcp select global//该接口收到DHCP报文后直接去DHCP服务器在全局配置的IP地址池中寻找一个可用的地址
     
    interface Ethernet0/0/1
     ip address 12.1.1.1 255.255.255.0  
     
    
     
    R2:
    dhcp enable
    dhcp server ping packet 10 timeout 100   //配置dhcp服务器分配某个ip之前先探测该ip是否已被网络中pc使用的功能，dhcp服务器会向该ip地址发10个包且每次100ms，无应答才会分配该ip地址给客户端
    dhcp check dhcp-rate enable           //开启dhcp的速率检测功能开关
    dhcp check dhcp-rate 90                //检测收到dhcp请求包的速率最大为90个/s，默认100个/s，防止dhcp泛红攻击
    ip pool network2
     gateway-list 192.168.2.1
     network 192.168.2.0 mask 255.255.255.0
     dns-list 192.168.2.1
     static-bind ip-address 192.168.2.10 mac-address 0001-1111-2222 //为固定mac分配固定ip
    interface Ethernet0/0/0
     ip address 12.1.1.2 255.255.255.0
    interface Ethernet0/0/1
     ip address 23.1.1.2 255.255.255.0
     dhcp select global
     
     
    R3：
    ehcp enable
    interface Ethernet0/0/0
     ip address 23.1.1.3 255.255.255.0
    interface Ethernet0/0/1
     ip address 192.168.2.1 255.255.255.0
     dhcp select relay //dhcp中继
     dhcp relay server-ip 23.1.1.2
     
##### 此时PC1可以动态获取到ip地址，而PC2不可以。因为R2没有到达192.168.2.1的路由，无法回应Offer报文  
    配置OPSF：  
    [R1]
    ospf 1
     area 0.0.0.0
      network 192.168.1.1 0.0.0.0
      network 12.1.1.1 0.0.0.0
      
    [R2]
    ospf 1
     area 0.0.0.0
      network 12.1.1.2 0.0.0.0
      network 23.1.1.2 0.0.0.0  
    [R3]
    ospf 1
     area 0.0.0.0
      network 192.168.2.1 0.0.0.0
      network 23.1.1.3 0.0.0.0
