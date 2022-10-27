---
title: "搭建自己的 Bitwarden 密码库"
date: 2022-10-22T23:10:31+08:00
categories: 
- Others
tags: 
- Bitwarden
- 密码管理
---

所有账户使用相同的密码是一个非常不安全的行为，一旦某个网站出现密码泄露或出售用户数据，其他网站的账号安全就无法得到保障。所以为不同的网站使用不同的密码是非常有必要的，如何为不同网站生成不同的高强度密码并正确记忆是一件比较困难的事情，这时使用密码管理软件是一个不错的选择。

目前市面上的密码管理软件五花八门，主流的密码管理软件有：1Password、LastPass、Bitwarden、KeePass 等。密码管理软件首先要考虑是否支持多平台和多平台同步，收费产品中 1Password、LastPass 免费套餐均无法支持多设备同步，收费套餐每月最低月付 3$，显然有些过于昂贵了。而开源产品 KeePass 的客户端在易用性上比起其他几家也要差了很多。后来了解到 Bitwarden 免费的套餐不仅可以支持多设备同步，而且客户端易用性也比较好，此外还支持通过自托管的方式部署密码管理服务。正好手上也有服务器，不妨将闲置的服务器资源用来搭建 Bitwarden 密码库。

## 部署自己的 Bitwarden 密码库

Bitwarden 官方提供的服务端程序的最低配置要求和推荐配置如下表所示，可以看到对服务器配置要求比较高。不过好在有第三方开发者使用 Rust 重写了 Bitwarden 服务端，并把它开源在了 GitHub，项目地址如下：[vaultwarden](https://github.com/dani-garcia/vaultwarden) 。

|                | Minimum                      | Recommended                  |
| -------------- | ---------------------------- | ---------------------------- |
| Processor      | x64, 1.4GHz                  | x64, 2GHz dual core          |
| Memory         | 2GB RAM                      | 4GB RAM                      |
| Storage        | 12GB                         | 25GB                         |
| Docker Version | Engine 19+ and Compose 1.24+ | Engine 19+ and Compose 1.24+ |

Vaultwarden 推荐使用 Docker 部署，这里我使用 Docker Compose 部署，在开始之前，首先确保部署 Bitwarden 的服务器上已经安装好 Docker 和 Docker Compose。

> Vaultwarden 也提供了完善的文档，详细配置可以参考[Vaultwarden Wiki](https://github.com/dani-garcia/vaultwarden/wiki)。

## 使用 Docker Compose 部署 Bitwarden 服务器

1. 使用 SSH 登录上服务器，在用户主目录下创建一个 `bitwarden` 文件夹作为容器启动目录用于存放 Bitwarden 数据文件。并在 `bitwarden` 文件夹下创建一个 `bw-data` 文件夹用于存储数据库文件。

```bash
cd ~ && mkdir bitwarden && cd bitwarden
mkdir bw-data
```

2. 在 bitwarden 文件夹下创建一个 `docker-compose.yml` 文件，文件内容如下：

```yml
version: "3"
services:
  bitwarden:
    image: vaultwarden/server:latest
    container_name: bitwardenrs
    restart: always
    ports:
      - "127.0.0.1:8080:80"
      - "127.0.0.1:3012:3012"
    volumes:
      - ./bw-data:/data
    environment:
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=true
      - WEB_VAULT_ENABLED=true
      - ADMIN_TOKEN=your_token
```

- Bitwarden 默认运行在 80 和 3012 端口，这里我将容器的 80 端口映射到服务器的 8080 端口，避免与服务器上的 Web 服务器端口冲突。端口可以根据需求修改。

- 注意 `SIGNUPS_ALLOWED` 要设置为 true，否则无法注册账号，如果你不希望别人使用你的服务，可以在注册完毕后，将 `SIGNUPS_ALLOWED` 禁用并重启容器。

- `WEB_VAULT_ENABLED` ：开启网页密码库。

- `ADMIN_TOKEN` : 用于登录 Bitwarden 服务器管理员后台的密码，设置后即可以通过 `https://domain/admin` 链接登录管理员后台。
3. 启动容器：

```bash
docker compose up -d
```

## 配置 Caddy 反向代理

此时 Bitwarden 服务器已经运行起来了，但是此时只能通过 HTTP 访问，HTTP 协议传输的内容都是没有加密的，我们的密码安全得不到保障，所以我们后面还需要通过 Web 服务器对 Bitwarden 进行反向代理，通过 HTTPS 访问 Bitwarden 密码库。

最常用的 Web 服务器有 Nginx，但是 Nginx 配置方式比较复杂，这里我使用 Caddy 服务器来实现反向代理，Caddy 是一款使用 Golang 实现的 Web 服务器，它提供了开箱即用的 HTTPS 配置方式，不需要我们自己申请证书。

根据 Linux 系统版本，按照 Caddy 文档的方式安装好 Caddy，然后修改 Caddy 的配置文件，配置文件的位置在 `/etc/caddy/Caddyfile`, 修改后的配置文件如下，记得将 domain 替换为实际的域名。

```
{
    admin off
}

(ssl) {
    header / Strict-Transport-Security "max-age=63072000"

    encode zstd gzip

    tls {
        protocols tls1.2 tls1.3
        ciphers TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
    }
}

domain {
    import ssl

    reverse_proxy /notifications/hub localhost:3012

    reverse_proxy localhost:8080 {
        header_up X-Real-IP {remote_host}
    }
}
```

然后使用 `sudo systemctl reload caddy` 重启 Caddy 服务器，此时就可以通过 `https://domain` 访问 Bitwarden 网页密码库。

## 使用 Bitwarden

访问 `https://domain` 注册一个账号，然后在官方渠道下载你使用的平台的客户端，这里以 Chrome 浏览器插件为例，其他平台操作类似。

首先点击拓展图标，在弹出的窗口的左上角有一个齿轮，单击它即可进入设置自托管环境的页面，在服务器 URL 输入框中填入 `https://domain` 然后保存，最后输入前面注册的账号，登录即可正常使用。

![](https://s2.loli.net/2022/10/23/snUqEM1IFC7oauH.png)
![](https://s2.loli.net/2022/10/23/1HeCjK6MZFsQ5zt.png)

## 更新 Bitwarden

Bitwarden 会不定期更新，建议间隔一段时间更新一下 Bitwarden 服务器。

```bash
cd ~/bitwarden
docker compose down
docker pull vaultwarden/server:latest
docker compose up -d
```

## 参考

- https://github.com/dani-garcia/vaultwarden/wiki
- https://juejin.cn/post/7130893489637851173