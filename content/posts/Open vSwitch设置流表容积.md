---
title: Open vSwitch 设置流表容积
date: 2022-03-01 16:12:01
tags:
- SDN
- OpenFlow
- Open vSwitch
---

在硬件交换机中，由于芯片大小的限制，流表的容量是一个有限的固定值。Open vSwitch (OVS) 是一个开源的虚拟交换机（软件交换机）。默认情况下，OVS 会根据系统状态自动设置流表的容量。
最近在研究过程中，需要模拟在不同流表容量下，不同驱逐方案对流表占用的减小情况,需要将 OVS 的容量设置为一个固定的值。查询了很多资料，没有找到相关的解决方案，最后在 OVS 的文档中找到了相关的条目。

> http://www.openvswitch.org/support/dist-docs/ovs-vsctl.8.html

在 OpenFlow 1.4 以前，当交换机流表资源耗尽后，新安装的流表项不会插入到流表中，交换机会向控制器返回一个错误信息。但是这会带来新的问题，因为控制器需要时间处理流表项，这可能导致服务中断。在 OpenFlow 1.4 及以后的版本中，引入了驱逐策略，它允许交换机能够自动驱逐重要性比较低的流表项，为新的流表项腾出空间。

在 OVS 的文档中介绍了如何设置交换机流表的容积和当流表溢出后交换机执行什么操作。在文中使用 `ovs-vsctl` 命令修改 `ovsdb` 中的交换机配置达到限制流表的容积。


以下命令将网桥（交换机）br0 的流表 0 所允许的最大流表项数目设置为 100，并当交换机流表溢出时，交换机会拒绝插入新的流表项。
```shell
ovs-vsctl -- --id=@ft  create  Flow_Table  flow_limit=100  overflow_policy=refuse -- set Bridge br0 flow_tables=0=@ft
```

以下命令将最大流表项设置为 100，当流量超过 100 个时， 让网桥 br0 上的流表 0 驱逐交换机中的流表项， 并根据匹配的入端口进行公平处理。

```shell
ovs-vsctl -- --id=@ft  create  Flow_Table  flow_limit=100  overflow_policy=evict  groups='"NXM_OF_IN_PORT[]"' -- set Bridge br0 flow_tables:0=@ft
```