---
layout: post
title: FlexE知识
tags: [Computer Networking]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# Flex Ethernet (FlexE)

灵活以太网技术（Flex Ethernet）是承载网实现业务隔离承载和网络分片的一种接口技术，近两年发展迅速，被各大标准组织广泛接纳。继FlexE在OIF形成标准后，2017年第一季度，由华为提出的FlexE for IP/MPLS标准正式在BBF立项。

回顾以太网的发展历程，第一代以太网Native Ethernet诞生于1980年，广泛应用于园区、企业以及数据中心的互联。第二代以太网Carrier Ethernet从2000年开始发展一直沿用到现在，主要面向运营商网络，广泛应用于电信级城域网、3G/4G移动承载网、专线接入等。随着5G时代的到来，云服务、AR/VR、车联网等新业务涌现，以太网技术进一步发展，第三代以太网称为灵活以太网即Flex Ethernet（简称FlexE）。

FlexE标准最早起源于 OIF接口物理层标准，因其具有带宽灵活可调、数据隔离、完美契合5G业务等特点，受到全球主流运营商、供应商的认可。

灵活以太网技术通过对以太网轻量级增强，在以太网L2（MAC）/L1（PHY）之间的中间层增加FlexE Shim层，Flex Shim层基于时分复用分发机制，将多个Client接口的数据按照时隙方式调度并分发至多个不同的子通道。以100GE管道为例，通过FlexE Shim可以划分为20个5G速率的子通道，每个Client侧接口可指定使用某一个或多个子通道，实现业务隔离。

FlexE能够实现大端口的捆绑功能，有效地解决之前网络带宽升级面临的问题。比如接入层50GE带宽能够满足5G初期的业务发展需求，随着5G的深入发展，接入层需升级至100GE，通过FlexE绑定功能，只需再扩容一个50GE端口，就能将接入层升级至100GE，不用进行大量的业务调整和割接工作，也能保护前期的投资。

FlexE分片是基于时隙调度将一个物理以太网端口划分为多个以太网弹性硬管道，使得网络既具备类似于TDM（时分复用）独占时隙、隔离性好的特性，又具备以太网统计复用、网络效率高的双重特点，实现同一分片内业务统计复用，分片之间业务互不影响，相对于通过VPN实现的分片隔离性更好，为5G网络分片提供了更多选择。

## 定义FlexE
```
ABSTRACT: The Flex Ethernet (FlexE) Implementation Agreement provides a generic mechanism for supporting a variety of Ethernet MAC rates that may or may not correspond to any existing Ethernet PHY rate. This includes MAC rates that are both greater than (through bonding) and less than (through sub-rate and channelization) the Ethernet PHY rates used to carry FlexE.This can be viewed as a generalization of the Multi-Link Gearbox implementation agreements,removing the restrictions on the number of bonded PHYs (MLG2.0, for example, supports one or two 100GBASE-R PHYs) and the constraint that the FlexE clients correspond to Ethernet rates (MLG2.0 supports only 10G and 40G clients)
```
FlexE提供了一种通用的机制，支持不同以太MAC速率，它可能响应任何存在的以太PHY速率。

## FlexE三种模式

FlexE完成协议支持三种通用的能力：

    绑定：支持一个200G MAC基于两个绑定的100G物理层通道；它的优点是支持高容量、长波长传输；
    速率划分：支持50G MAC基于100g物理通道传输；它的优点是提高网络速率；
    物理层交通分导：支持一个150G和两个25G MACs基于两个绑定的100GBASE-R PHYs传输；它的优点是更简洁，能提供更多可测量服务管理；

![FlexE三种模式](https://img-blog.csdn.net/20170606205358394?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGlucXVhbnYx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## FlexE通用结构

FlexE Group：指一组从1到n绑定的以太网PHYs
FlexE Clients：一个基于物理地址的以太网流（有可能不与实际以太网物理接口速率匹配）

![FlexE通用结构](https://img-blog.csdn.net/20170606205430303?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGlucXVhbnYx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)