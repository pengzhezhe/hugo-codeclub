---
layout: post
title:  "OpenFlow 端口字段解析"
date: 2021/05/15
categories: 
- SDN
tags: 
- OpenFlow
---

# OpenFlow Ports

OpenFlow ports are the network interfaces for passing packets between OpenFlow processing and the rest of the network. OpenFlow switches connect logically to each other via their OpenFlow ports, a packet can be forwarded from one OpenFlow switch to another OpenFlow switch only via an output OpenFlow port on the first switch and an ingress OpenFlow port on the second switch.

An OpenFlow switch must support three types of OpenFlow ports: *physical ports*, *logical ports* and *reserved ports*.

## Physical Ports

The OpenFlow physical ports are switch defined ports that correspond to a hardware interface of the switch. For example, on an Ethernet switch, physical ports map one-to-one to the Ethernet interfaces.

In some deployments, the OpenFlow switch may be virtualised over the switch hardware. In those cases, an OpenFlow physical port may represent a virtual slice of the corresponding hardware interface of the switch.

## Logical Ports
The OpenFlow logical ports are switch defined ports that don’t correspond directly to a hardware interface of the switch. Logical ports are higher level abstractions that may be defined in the switch using non-OpenFlow methods (e.g. link aggregation groups, tunnels, loopback interfaces). 

Logical ports may include packet encapsulation and may map to various physical ports. The processing done by the logical port is implementation dependent and must be transparent to OpenFlow processing, and those ports must interact with OpenFlow processing like OpenFlow physical ports. 

The only differences between physical ports and logical ports is that a packet associated with a logical port may have an extra pipeline field called Tunnel-ID associated with it and when a packet received on a logical port is sent to the controller, both its logical port and its underlying physical port are reported to the controller.

## Reserved Ports

The OpenFlow reserved ports are defined by this specification. They specify generic forwarding actions such as sending to the controller, flooding, or forwarding using non-OpenFlow methods, such as “normal” switch processing.

A switch is not required to support all reserved ports, just those marked “Required” below.

- *Required*: **ALL**: Represents all ports the switch can use for forwarding a specific packet. Can be used only as an output port. In that case a copy of the packet is sent to all standard ports, excluding the packet ingress port and ports that are configured OFPPC_NO_FWD.

- *Required*: **CONTROLLER**: Represents the control channel with the OpenFlow controllers. Can be used as an ingress port or as an output port. When used as an output port, encapsulates the packet in a packet-in message and sends it using the OpenFlow switch protocol. When used as an ingress port, this identifies a packet originating from the controller.

- *Required*: **TABLE**: Represents the start of the OpenFlow pipeline. This port is only valid in an output action in the list of actions of a packet-out message, and submits the packet to the first flow table so that the packet can be processed through the regular OpenFlow pipeline.

- *Required*: **IN_PORT**: Represents the packet ingress port. Can be used only as an output port, sends the packet out through its ingress port.

- *Required*: **ANY**: Special value used in some OpenFlow requests when no port is specified (i.e. port is wildcarded). Some OpenFlow requests contain a reference to a specific port that the request only applies to. Using ANY as the port number in these requests allows that request instance to apply to any and all ports. Can neither be used as an ingress port nor as an output port.

- *Optional*: **LOCAL**: Represents the switch’s local networking stack and its management stack. Can be used as an ingress port or as an output port. The local port enables remote entities to interact with the switch and its network services via the OpenFlow network, rather than via a separate control network. With an appropriate set of flow entries, it can be used to implement an in-band controller connection (this is outside the scope of this specification).

- *Optional*: **NORMAL**: Represents forwarding using the traditional non-OpenFlow pipeline of the switch. Can be used only as an output port and processes the packet using the normal pipeline. In general will bridge or route the packet, however the actual result is implementation dependent. If the switch cannot forward packets from the OpenFlow pipeline to the normal pipeline, it must indicate that it does not support this action.

- *Optional*: **FLOOD:**: Represents flooding using the traditional non-OpenFlow pipeline of the switch. Can be used only as an output port, actual result is implementation dependent. In general will send the packet out all standard ports, but not to the ingress port, nor ports that are in OFPPS_BLOCKED state. The switch may also use the packet VLAN ID or other criteria to select which ports to use for flooding.

## Reference

[OpenFlow Switch Specification Ver 1.3.5](https://opennetworking.org/wp-content/uploads/2014/10/openflow-switch-v1.3.5.pdf)