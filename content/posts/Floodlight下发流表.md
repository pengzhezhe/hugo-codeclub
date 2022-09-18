---
layout: post
title:  "Floodlight 下发流表"
date: 2021-06-20
categories: 
- SDN
tags: 
- OpenFlow
- Floodlight
---

Floodlight 中下发流表的方法主要有以下几种方式

- 使用 REST API 下发流表
- 使用 StaticEntryPusherService Java API 下发流表
- 手动构建 FlowMod 消息发送到交换机

## 使用 REST API 下发流表

Add flow entry

```shell
curl -X POST -d '{"switch":"00:00:00:00:00:00:00:01", "name":"flow-mod-1", "cookie":"0", "priority":"32768", "in_port":"1","active":"true", "actions":"output=2"}' http://<controller_ip>:8080/wm/staticentrypusher/json
```

Add group entry

```shell
curl -X POST -d '{"switch":"00:00:00:00:00:00:00:01", "entry_type":"group", "name":"group-mod-1", "active":"true", "group_type":"select", "group_id":"1",  "group_buckets":[ {"bucket_id":"1", "bucket_watch_group":"any", "bucket_weight":"50", "bucket_actions":"output=2"}, {"bucket_id":"2", "bucket_watch_group":"any", "bucket_weight":"50", "bucket_actions":"output=3"} ]}' http://<controller_ip>:8080/wm/staticentrypusher/json
```

## 使用 StaticEntryPusherService

```java
IOFSwitch sw
OFFactory ofFactory = sw.getOFFactory();
Match match = ofFactory.buildMatch()
    .setExact(MatchField.ETH_TYPE, EthType.IPv4)
    .setExact(MatchField.IPV4_SRC, iPv4.getSourceAddress())
    .setExact(MatchField.IPV4_DST, iPv4.getDestinationAddress())
    .setExact(MatchField.IP_PROTO, IpProtocol.ICMP)
    .build();
//Use StaticEntryPusher
OFFlowAdd flowAdd = ofFactory.buildFlowAdd()
    .setBufferId(OFBufferId.NO_BUFFER)
    .setPriority(32767)
    .setIdleTimeout(0)
    .setHardTimeout(0)
    .setMatch(match)
    .build();
staticEntryPusherService.addFlow("flow_name", flowAdd, sw.getId());
```

## 手动构建 FlowMod 消息

```java
IOFSwitch sw
OFFactory ofFactory = sw.getOFFactory();
Match match = ofFactory.buildMatch()
    .setExact(MatchField.ETH_TYPE, EthType.IPv4)
    .setExact(MatchField.IPV4_SRC, iPv4.getSourceAddress())
    .setExact(MatchField.IPV4_DST, iPv4.getDestinationAddress())
    .setExact(MatchField.IP_PROTO, IpProtocol.ICMP)
    .build();
List<OFAction> actions = new ArrayList<>();
OFInstructionApplyActions ofInstructionApplyActions = ofFactory.instructions().applyActions(actions);
//设备流表的属性
OFFlowAdd flowAdd = ofFactory.buildFlowAdd()
    .setCookie(U64.ZERO)
    .setBufferId(OFBufferId.NO_BUFFER)
    .setPriority(32767)
    .setIdleTimeout(0)
    .setHardTimeout(0)
    .setMatch(match)		
    .setInstructions(Collections.singletonList(ofInstructionApplyActions))
    .setTableId(TableId.ZERO)
    .build();
sw.write(flowAdd);
```

