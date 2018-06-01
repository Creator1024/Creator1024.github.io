---
title: Ipsec
toc: true
date: 2018-05-25 22:25:18
tags: 
- huawei
- IPSec
- ospf 
categories:
- 数据通信
- R&S配置
- 华为
---




![图片.png](https://upload-images.jianshu.io/upload_images/12329802-035c3a2c41fb8a46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 1.配置基本信息
```
AR1和AR3都写入一条默认路由模拟到达外网
[AR1]ip route-static 0.0.0.0 0.0.0.0 12.1.1.2
[AR3]ip route-static 0.0.0.0 0.0.0.0 23.1.1.2
```
##### 2.配置IPSec
```
总部路由器（AR1）配置
1.抓取内网流量
[AR1]acl 3000
[AR1-acl-adv-3000]rule 5 permit ip source 192.168.1.0 0.0.0.255 destination 192.
168.2.0 0.0.0.255

2.配置ike
[AR1]ike proposal 1
[AR1-ike-proposal-1]encryption-algorithm 3des-cbc
[AR1-ike-proposal-1]authentication-algorithm md5
[AR1-ike-proposal-1]quit

[AR1]ike peer AR3 v1
[AR1-ike-peer-AR3]pre-shared-key simple oneWay //设置协商密钥，隧道两端要一致
[AR1-ike-peer-AR3]ike-proposal 1 //调用刚刚创建的ike
[AR1-ike-peer-AR3]remote-address 23.1.1.3 

3.配置IPSec
[AR1]ipsec proposal 1
[AR1-ipsec-proposal-1]transform ah

[AR1]ipsec policy Test 10 isakmp
[AR1-ipsec-policy-isakmp-Test-10]security acl 3000 //isakmp使用ike建立ipsec SA
[AR1-ipsec-policy-isakmp-Test-10]ike-peer AR3
[AR1-ipsec-policy-isakmp-Test-10]proposal 1

4.接口下调用ipsec policy
[AR1-GigabitEthernet0/0/1]ipsec policy Test
```

```
分部路由器（AR3）配置
[AR3]acl 3000
[AR3-acl-adv-3000]rule 5 permit ip source 192.168.2.0 0.0.0.255 destination 192.
168.1.0 0.0.0.255
[AR3]ike proposal 1
[AR3-ike-proposal-1]encryption-algorithm 3des-cbc 
[AR3-ike-proposal-1]authentication-algorithm md5

[AR3]ike peer AR1 v1
[AR3-ike-peer-AR1]pre-shared-key simple oneWay
[AR3-ike-peer-AR1]ike-proposal 1
[AR3-ike-peer-AR1]remote-address 12.1.1.1


[AR3]ipsec proposal 1
[AR3-ipsec-proposal-1]transform ah

[AR3]ipsec policy Test 10 isakmp 
[AR3-ipsec-policy-isakmp-Test-10]security acl 3000
[AR3-ipsec-policy-isakmp-Test-10]ike-peer AR1
[AR3-ipsec-policy-isakmp-Test-10]proposal 1
[AR3-ipsec-policy-isakmp-Test-10]quit

[AR3-GigabitEthernet0/0/0]ipsec policy Test  
```
##### 3.验证结果
若配置错误，display不会出现以下信息：  
![图片.png](https://upload-images.jianshu.io/upload_images/12329802-840fdfacd0e501b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

PC1成功ping通PC3：     
![图片.png](https://upload-images.jianshu.io/upload_images/12329802-df2374166c337940.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![图片.png](https://upload-images.jianshu.io/upload_images/12329802-856ee8d5980d6e3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 参考资料
> - []()
> - []()
