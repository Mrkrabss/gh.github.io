---
layout: post
title: shadowsocks-android 工作原理（一）
date: 2019-08-08 08:56:49
tags: shadowsocks
category: vpn
---
### shadowsocks-android 三种工作模式

1. VPN 模式

VPN模式是shadowsocks-android默认的工作模式，使用该模式时，会启动VpnService服务、TUN2SOCKS子进程、SS_LOCAL子进程。

- VpnService 

通过启用Android提供的VpnService类，Android将手机上所有流量导入TUN虚拟设备，同时给shadowsocks-android应用提供一个文件描述符，通过该文件描述符，shadowsocks-android可向Android系统读取和写入ip包。

- TUN2SOCKS

上文提到shadowsocks-android可向系统读写ip包，此时工作在网络层，ss协议无法直接处理ip包，所以需要TUN2SOCKS程序将ip包转为Socks协议的数据。

- SS_LOCAL 

SS_LOCAL程序可处理Socks协议的数据，会读出TUN2SOCKS转换后的数据，通过ss协议加密发送到远程vpn服务器；当远程服务器有数据返回时，解密后写入TUN2SOCKS，TUN2SOCKS转换成ip包，写入TUN虚拟设备，TUN虚拟设备分发至手机上的各应用。

2. 仅代理 模式

仅代理模式只启动SS_LOCAL子进程，SS_LOCAL开启的默认端口1080，需要支持SOCKS5的应用手动配置到该端口，便可使用VPN服务。

3. 透明代理 模式

透明代理模式启动了SS_LOCAL子进程和REDSOCKS子进程，REDSOCKS默认端口8200，仅支持转发TCP流量，需要使用TCP通讯的应用手动配置到该端口，便可使用VPN服务。

