---
title: 通过内网穿透搭建 Risk of Rain 2 私服
date: 2022-03-16 20:41:11
categories: 
- Others
tags:
- Risk of Rain 2
- 内网穿透
---


这篇文章将讲解如何通过内网穿透在本机运行 Risk of Rain 2 服务器，允许非局域网的玩家加入到同一局游戏中。

## 必要条件

- 一台拥有公网IP的服务器
- 一台可以运行 Risk of Rain 2 Dedicated Server 的 Windows 主机

## 搭建 Risk of Rain 2 私服

搭建的私服可以运行在你玩游戏的主机上，也可以是一台局域网中没有公网IP的主机，这里我在玩 Risk of rain 2 的主机上同时运行私服。

1. 在 Steam 库中搜索 `Risk of Rain 2 Dedicated Server`，然后下载该工具。

2. 下载完毕后，在 Steam 库中可以找到运行私服的可执行文件，右键点击 `Risk of Rain 2 Dedicated Server` 选择 `浏览本地文件` ，然后在此目录下找到 `\Risk of Rain 2_Data\Config` 文件夹，然后创建一个文件名为 `server.cfg` 的文件，文件的详细内容如下:
```
sv_maxplayers 4;
steam_server_heartbeat_enabled 1;
sv_hostname "Risk of Rain 2 Dedicated Server";
sv_port 27015;
steam_server_query_port 27016;
steam_server_steam_port 0;
sv_password "server_password";

echo "Loaded custom server configuration.";
```

> sv_maxplayers, sv_hostname, sv_password 可以根据需要修改
> sv_maxplayers指的是服务器允许加入的最多玩家数量
> sv_hostname 指的是你的服务器显示在服务器列表中的名字
> sv_password 是玩家加入你的服务器需要输入的密码
> 更多关于配置文件的设置可以查看该目录下的 `server_startup.cfg` 文件中的相关内容

3. 启动 Steam 库中的 Risk of Rain 2 Dedicated Server，等待服务器启动完毕，可以在 Steam 的 查看-> 服务器 -> 局域网 中看到你的服务器。如果你和你的小伙伴在一个局域网中就可以直接通过此服务器开始游戏，如果你和你的小伙伴不在一个局域网中，那么就需要进行下一步，将你的服务器通过内网穿透部署到公网中。

## 通过 frp 内网穿透

这里我们需要用到一个开源软件 [frp](https://github.com/fatedier/frp)，同时在你的主机和拥有公网IP的服务器上安装 frp 的服务器端和客户端程序。

### 服务器端配置

1. 在服务器上下载 [Linux 版的 frp 程序](https://github.com/fatedier/frp/releases/download/v0.40.0/frp_0.40.0_linux_amd64.tar.gz)

2. 解压得到 frps 可执行文件，把 frps 复制到 `/usr/bin/` 目录下

```sh
sudo cp frps /usr/bin/
```

3. 把 frps 配置文件 `frps.ini` 复制到 `/etc/frp/` 目录下, `frps.ini` 的内容如下，其中 `token` 为 frp 服务端和客户端通信的密码，防止服务器被滥用。

```sh
sudo mkdir /etc/frp
sudo cp frps.ini /etc/frp/
```

```ini
[common]
bind_port = 7000
authentication_method = token
token = 87654321
```

4. 为了让 `frps` 一直在后台运行，我们使用 systemd 来管理 frps 服务，将 `frps.service` 复制到 `/usr/lib/systemd/system/` 目录下，`frps.service` 的内容如下

```sh
sudo cp frps.service /usr/lib/systemd/system/
```

```ini
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frps -c /etc/frp/frps.ini
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

5. 启动 frps 服务

```sh
sudo systemctl start frps
```

> 特别注意：如果你使用的是腾讯云、阿里云等云服务器，记得在控制台开放服务器的 7000, 27015, 27016 端口

### 客户端配置

1. 在运行Risk of Rain 2 服务器的主机上下载 [Windows 版的 frp 程序](https://github.com/fatedier/frp/releases/download/v0.40.0/frp_0.40.0_windows_amd64.zip)

2. 解压得到 frpc.exe 文件，在同一目录下创建一个 `frpc.ini` 配置文件，文件的内容如下，修改 `common` 标签下的 `server_addr` 为服务器的公网IP地址，`token` 为 frp 服务端和客户端通信的密码，要和上面服务器中配置的密码保持一致。

```ini
[common]
server_addr = server_addr
server_port = 7000
token = 87654321

[ROR1]
type = udp
local_ip = 127.0.0.1
local_port = 27015
remote_port = 27015

[ROR2]
type = udp
local_ip = 127.0.0.1
local_port = 27016
remote_port = 27016
```

3. 启动 frpc

```sh
./frpc.exe -c ./frpc.ini
```

## 邀请朋友加入私服

打开 Risk of Rain 2 客户端后，在主页面按下 `Ctrl` + `Alt` + `~` 打开控制台，在控制台的输入框输入以下命令，将其中的 `server_password` 替换为搭建私服章节中设置的私服密码，将 `server_addr` 替换为开启 frps 服务的服务器的公网IP。

```
cl_password "server_password"; connect "server_addr:27015"
```

接下来就可以享受低延迟的游戏体验了。