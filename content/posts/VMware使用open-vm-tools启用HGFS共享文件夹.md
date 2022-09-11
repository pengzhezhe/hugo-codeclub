---
title: VMware 使用 open-vm-tools 启用 HGFS 共享文件夹
date: 2022-04-28
categories: 
- Linux
tags: 
- Ubuntu
- VMWare
---

最近 Ubuntu 最新的 LTS 版本 22.04 发布了，抱着尝鲜的想法，想将我手上的 Linux 实验环境升级到 Ubuntu 22.04。

我自己目前使用的 Ubuntu 版本是 20.04，在 20.04 中执行 `do-release-upgrade` 命令，系统提示没有更新的 LTS 版本。由于没办法直接升级，我直接重新在 VMware 上重新创建了一台虚拟机用来安装 Ubuntu 22.04 LTS，安装过程和之前的版本基本无差异。

安装完后，照例配置各种开发环境，不清楚是 Ubuntu 的适配问题还是 VMware 的问题，在 22.04 上无法正常安装 VMware tools，遂通过安装 open-vm-tools 来替代。

下面介绍如何在 Ubuntu 22.04 LTS 上安装 `open-vm-tools` 并启动共享文件夹。

1. 创建一个服务，让系统自动挂载 HGFS。在`/etc/systemd/system`下创建一个`mnt-hgfs.mount`文件，文件的内容如下：

```ini
[Unit]
Description=VMware mount for hgfs
DefaultDependencies=no
Before=umount.target
ConditionVirtualization=vmware
After=sys-fs-fuse-connections.mount

[Mount]
What=vmhgfs-fuse
Where=/mnt/hgfs
Type=fuse
Options=default_permissions,allow_other

[Install]
WantedBy=multi-user.target
```

2. 创建包含以下内容的文件 `/etc/modules-load.d/open-vm-tools.conf`：

```
fuse
```

3. 使用以下命令启用系统服务,这可确保重新引导后会挂载 hgfs 目录。

```bash
sudo systemctl enable mnt-hgfs.mount
```

4. 在 VMware 的设置中，启用虚拟机的共享文件夹，并设置要共享的文件夹。

5. 共享文件夹应显示在目录 /mnt/hgfs 中。否则，使用以下命令启动服务：

```bash
sudo systemctl start mnt-hgfs.mount
```

参考文章

- https://kb.vmware.com/s/article/74650?lang=zh_CN