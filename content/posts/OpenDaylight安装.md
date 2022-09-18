---
title:  OpenDaylight 安装
date: 2021-12-08
categories: 
- SDN
tags: 
- OpenFlow
- OpenDaylight
---

# 安装 OpenDaylight Oxygen 版本

## 下载 OpenDaylight

到 OpenDaylight官方存储库中[下载](https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/karaf/0.8.4/karaf-0.8.4.zip)

更多详情请参考 [OpenDaylight Oxygen 文档](https://docs.opendaylight.org/en/stable-oxygen/index.html)

## 启动 OpenDaylight

解压 `karaf-0.8.4.zip`，定位到 `karaf/bin` 目录下，运行 `karaf.bat` 脚本。

## 安装支持 OpenFlow 的相关 Feature

- ODL 依赖: odl-restconf-all 
- OpenFlow: odl-openflowplugin-flow-services-ui
- L2 Switch: odl-l2switch-switch-ui 
- Web 管理页面: odl-dluxapps-applications

安装相关 Feature

```shell
feature:install odl-restconf-all odl-openflowplugin-flow-services-rest odl-l2switch-switch-ui odl-dluxapps-applications
```

## 使用 Mininet 验证

启动一个简单的拓扑

```shell
sudo mn --topo linear,3 --controller remote,ip=192.168.126.1,port=6653 --switch ovsk,protocols=OpenFlow13
```

运行 `pingall` 命令，验证主机之间是否可以 ping 通。