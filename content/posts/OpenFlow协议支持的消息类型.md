---
layout: post
title:  OpenFlow 协议支持的消息类型
date: 2020-10-17
tags: 
- SDN
- OpenFlow
---

OpenFlow 被认为是第一个软件定义网络（SDN）标准之一。它最初在 SDN 环境中定义了通信协议，使 SDN 控制器能够与物理和虚拟的交换机和路由器等网络设备的转发平面直接进行交互，从而更好地适应不断变化的业务需求。

OpenFlow 的消息类型主要包括三大类 Controller‐to‐Switch、Asynchronous、Symmetric。

## Controller to Switch 消息

- **Features**: 用于控制器请求交换机身份和基本能力信息，且交换机必须对此类消息进行应答。features消息通常在安全通道建立时执行。
- **Configuration**: 控制器设置或查询交换机上的配置信息，交换机仅需要应答查询信息。
- **Modify-State**: 控制器通过发送Modify-State消息管理交换机流表项和端口状态等。该命令主要用于增加、删除、修改OpenFlow交换机内的流表表项，组表表项以及交换机端口的属性。
- **Read-State**: Read-State消息用于控制器收集交换机上的各种信息，包括配置、统计等信息。
- **Packet-Out**: 用于控制器通过交换机指定端口转发数据包。
- **Barrier**: 用于控制器确保消息依赖满足，或接收完成操作的通知。
- **Role-Request**: 用于控制器设置或查询安全通道的角色信息。当交换机连接多个控制器时，此类消息是非常有用的。

## 异步(Asychronous)消息（Switch-to-Controller）

- **Packet-In**: 交换机收到一个网络数据包，在流表中没有匹配项，则发送Packet-In消息给控制器。如果交换机缓存足够多，网络数据包被临时存放在缓存中，网络数据包部分内容和在交换机缓存中的序号一同发给控制器；如果交换机缓存不足则将整个数据包发送给控制器。
- **Flow-Removed**: 交换机中的流表因为超时或者修改等原因被删除掉，会触发flow-removed消息。
- **Port-Status**: 交换机端口状态发送变化时触发port-status消息。
- **Error**: 交换机发生故障时触发error消息。

## 同步(Symmetric)消息

- **Hello**: 用于交换机和控制器建立连接，当连接启动时交换机和控制器会发送Hello交互。
- **Echo**: 用于验证控制器与交换机之间连接的存活，控制器和OpenFlow交换机都会发送Echo Request/Reply消息。对于接收到的Echo Request消息必须能返回Echo Reply消息。Echo消息也可用于测量控制器与交换机之间链路的延迟和带宽。。
- **Experimenter（Vendor）**: 用于在OpenFlow消息类型空间中为OpenFlow交换机提供额外的功能。

## 参考资料

- https://www.h3c.com/cn/d_201811/1131080_30005_0.htm
- https://chentingz.github.io/2019/12/30/%E3%80%8COpenFlow%E3%80%8D%E5%8D%8F%E8%AE%AE%E5%85%A5%E9%97%A8/
