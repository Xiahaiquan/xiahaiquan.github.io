---
title: Nmap的简单使用
date: 2019-09-26 20:55:23
tags: 
---



# 安装

## 客户端安装

### 打开官网链接

<!-- more -->

[官网地址](https://nmap.org/download.html)

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-1.png)

​																		图 1-1 官网下载Mac版地址

### 下载客户端，安装完成

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-2.png)

​																			图 1-2 客户端终端图标

## 终端安装

命令：`brew install nmap`

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-3.png)

​																图 1-3 安装完成后再次安装后的终端输出

#  使用

## 在终端的使用 

### 扫描主机所有端口 

命令：` nmap 116.7.249.146`

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-4.png)

​																			图 1-4 扫描主机的输出

### 扫描指定主机的开放端口，系统版本等信息

命令： `nmap -A 192.168.100.52`

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-5.png)

​																图 1-5 扫描开发端口的详细输出

[更多命令的高级用法](https://www.cnblogs.com/nmap/p/6232969.html)， [更多扫描方式](https://www.cnblogs.com/c4isr/archive/2012/12/07/2807491.html)

## 客户端使用

### 扫描所有主机所有端口 

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-6.png)

​																	图 2-1 扫描结果标准输出

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-7.png)

 																	图 2-2 扫描结果图形化显示

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-8.png)

​																		图 2-3 主机明细显示

- 扫描结果导出建议使用XML格式，其中开头有乱码。PDF或其他格式不能正常显示。

[更多命令的高级用法](https://www.cnblogs.com/nmap/p/6232969.html)， [更多扫描方式](https://www.cnblogs.com/c4isr/archive/2012/12/07/2807491.html)

### 扫描出的状态说明

`Open`：端口处于开放状态，例如：当nmap使用TCP SYN对目标主机某一范围的端口进行扫描时，我们知道 TCP SYN报文是TCP建立连接的第一步，所以，如果目标主机返回SYN+ACK的报文，我们就认为此端口开放了并且使用了TCP服务。

`Closed`：端口处于关闭状态。例如：TCP SYN类型的扫描，如果返回RST类型的报文，则端口处于管理状态。这里我们值得注意的是关闭的端口也是可访问的，只是没有上层的服务在监听这个端口，而且，只是在我们扫描的这个时刻为关闭，当我们在另一个时间段进行扫描的时候，这些关闭的端口可能会处于open的状态。

`Filtered（过滤的）`：由于报文无法到达指定的端口，nmap不能够决定端口的开放状态，这主要是由于网络或者主机安装了一些防火墙所导致的。当nmap收到icmp报文主机不可达报文（例如：type为3，code为13（communication administratively prohibit）报文）或者目标主机无应答，常常会将目标主机的状态设置为filtered。

`Unfiltered（未被过滤的）`，当nmap不能确定端口是否开放的时候所打上的状态，这种状态和filtered的区别在于：unfiltered的端口能被nmap访问，但是nmap根据返回的报文无法确定端口的开放状态，而filtered的端口直接就没就没能够被nmap访问。端口被定义为Unfilterd只会发生在TCP ack扫描类型时当返回RST的报文。而端口被定义为filtered 状态的原因是是报文被防火墙设备，路由器规则，或者防火墙软件拦截，无法送达到端口，这通常表现为发送NMAP的主机收到ICMP报错报文，如：TYPE为3，code为13的报文（通信被认为的禁止 communication administratively prohibited），或者主机通过多次重复发送没有收到任何回应）。

Open\|filtered状态，这种状态主要是nmap无法区别端口处于open状态还是filtered状态。这种状态只会出现在open端口对报文不做回应的扫描类型中，如：udp，ip protocol ，TCP null，fin，和xmas扫描类型。

`Closed\|filtered状态`，这种状态主要出现在nmap无法区分端口处于closed还是filtered时。此状态只会出现在IP ID idle scan（这个类型我现在也不太理解，过段时间进行总结一些）中。

### 快速扫描TCP端口 

![](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUseTheNmap/1-9.png)

​																							图 2-4 扫描TCP端口