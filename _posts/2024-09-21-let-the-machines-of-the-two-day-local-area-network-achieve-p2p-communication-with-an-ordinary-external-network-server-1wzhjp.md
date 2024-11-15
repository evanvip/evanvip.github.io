---
layout: post
title: 让两台异地局域网的机器，借助一台普通的外网服务器实现p2p通讯！
date: '2024-09-21 09:39:09'
permalink: >-
  /post/let-the-machines-of-the-two-day-local-area-network-achieve-p2p-communication-with-an-ordinary-external-network-server-1wzhjp.html
tagline: >-
  使用 WireGuard 来实现 NAS1、MacBook 和 S1 之间的直接 P2P 通讯是完全可行的。WireGuard 是一个现代的、快速的、安全的
  VPN 协议，非常适合用于点对点（P2P）连接。以下是一个技术部署方案，帮助你实现这个目标。
published: true
---



要让两个无公网IP的设备（NAS1和MacBook）通过P2P方式进行快速数据通讯，同时利用公网服务器S1作为中继，并且不想登录或注册任何平台的互联网账号，只打算使用一台普通的公网服务器的 S1 ，那么你可以考虑以下几种工具和方法来实现你的需求：

使用 WireGuard 来实现 NAS1、MacBook 和 S1 之间的直接 P2P 通讯是完全可行的。WireGuard 是一个现代的、快速的、安全的 VPN 协议，非常适合用于点对点（P2P）连接。以下是一个技术部署方案，帮助你实现这个目标。

### 1. 安装 WireGuard

首先，你需要在每个设备上安装 WireGuard。

#### 在 NAS1 上安装 WireGuard

* **Synology NAS**: 如果你使用的是 Synology NAS，可以通过套件中心安装 WireGuard。
* **其他 NAS**: 你可能需要通过 SSH 连接到 NAS，并手动安装 WireGuard。

#### 在 MacBook 上安装 WireGuard

* 你可以通过 Homebrew 安装 WireGuard：

  ```bash
  brew install wireguard-tools
  ```

#### 在 S1 上安装 WireGuard

* 根据 S1 的操作系统，选择合适的安装方法。例如，如果是 Linux 系统，可以使用包管理器安装：

  ```bash
  sudo apt-get install wireguard
  ```

### 2. 生成密钥对

在每个设备上生成 WireGuard 的公钥和私钥。

```bash
wg genkey | tee privatekey | wg pubkey > publickey
```

### 3. 配置 WireGuard

在每个设备上创建并配置 WireGuard 配置文件。假设你为每个设备分配的 IP 地址如下：

* NAS1: `10.0.0.1/24`​
* MacBook: `10.0.0.2/24`​
* S1: `10.0.0.3/24`​

#### NAS1 配置 (`/etc/wireguard/wg0.conf`​)

```ini
[Interface]
Address = 10.0.0.1/24
PrivateKey = <NAS1_PRIVATE_KEY>
ListenPort = 51820

[Peer]
PublicKey = <MACBOOK_PUBLIC_KEY>
AllowedIPs = 10.0.0.2/32

[Peer]
PublicKey = <S1_PUBLIC_KEY>
AllowedIPs = 10.0.0.3/32
```

#### MacBook 配置 (`/etc/wireguard/wg0.conf`​)

```ini
[Interface]
Address = 10.0.0.2/24
PrivateKey = <MACBOOK_PRIVATE_KEY>
ListenPort = 51820

[Peer]
PublicKey = <NAS1_PUBLIC_KEY>
AllowedIPs = 10.0.0.1/32
Endpoint = <NAS1_PUBLIC_IP>:51820

[Peer]
PublicKey = <S1_PUBLIC_KEY>
AllowedIPs = 10.0.0.3/32
```

#### S1 配置 (`/etc/wireguard/wg0.conf`​)

```ini
[Interface]
Address = 10.0.0.3/24
PrivateKey = <S1_PRIVATE_KEY>
ListenPort = 51820

[Peer]
PublicKey = <NAS1_PUBLIC_KEY>
AllowedIPs = 10.0.0.1/32
Endpoint = <NAS1_PUBLIC_IP>:51820

[Peer]
PublicKey = <MACBOOK_PUBLIC_KEY>
AllowedIPs = 10.0.0.2/32
```

### 4. 启动 WireGuard

在每个设备上启动 WireGuard 并设置为开机自启。

#### 在 NAS1 上

```bash
sudo wg-quick up wg0
```

#### 在 MacBook 上

```bash
sudo wg-quick up /etc/wireguard/wg0.conf
```

#### 在 S1 上

```bash
sudo wg-quick up wg0
```

### 5. 测试连接

在每个设备上测试连接是否成功。你可以使用 `ping`​ 命令来测试连接。

```bash
ping 10.0.0.1  # 从 MacBook 或 S1 测试连接到 NAS1
ping 10.0.0.2  # 从 NAS1 或 S1 测试连接到 MacBook
ping 10.0.0.3  # 从 NAS1 或 MacBook 测试连接到 S1
```

### 6. 配置防火墙（可选）

确保每个设备的防火墙允许 WireGuard 的端口（默认是 51820）通过。

### 7. 持久化配置

确保 WireGuard 在设备重启后自动启动。你可以通过以下命令设置：

```bash
sudo systemctl enable wg-quick@wg0
```

### 总结

通过以上步骤，你可以在 NAS1、MacBook 和 S1 之间建立一个直接的 P2P 连接。WireGuard 的配置相对简单，性能也非常出色，适合用于家庭或小型办公室的网络连接。

要确定 NAS1 和 MacBook 之间的通信是否通过 S1 作为中继，还是直接的点对点通信，可以通过以下几种方式进行验证：

### 1. **网络拓扑检查**

* **物理连接**：检查 NAS1 和 MacBook 之间的物理连接。如果它们直接通过网线连接到同一个交换机或路由器，那么它们可能是直接通信的。
* **网络拓扑图**：查看网络拓扑图，确认 NAS1 和 MacBook 是否在同一个子网内，并且没有经过 S1 这样的中继设备。

### 2. **网络抓包分析**

* **Wireshark**：使用 Wireshark 在 NAS1 或 MacBook 上进行抓包分析。查看数据包的源地址和目标地址，确认它们是否直接通信，而不是经过 S1。
* **过滤器**：在 Wireshark 中使用过滤器，只捕获 NAS1 和 MacBook 之间的通信数据包，进一步确认是否存在中继设备。

### 3. **路由表检查**

* **路由表**：在 NAS1 和 MacBook 上查看路由表，确认它们之间的通信路径。如果路由表显示它们直接通信，没有经过其他设备，那么它们是点对点通信。
* ​**​`traceroute`​**​ **或** **​`tracert`​**​：使用 `traceroute`​（Linux/Mac）或 `tracert`​（Windows）命令，追踪数据包的路径。如果路径中没有 S1，那么它们是直接通信的。

### 4. **网络设备配置检查**

* **交换机/路由器配置**：检查 S1 或其他网络设备的配置，确认它们是否被配置为中继设备。如果 S1 没有配置为中继设备，那么 NAS1 和 MacBook 可能是直接通信的。

‍

‍

你好！我是Kimi，由一群充满热情的科学家和开发者创造的人工智能助手。我擅长中英文对话，能够提供安全、有帮助、准确的回答。无论你需要阅读文件、搜索信息还是进行日常对话，我都在这里为你服务。现在，有什么可以帮你的吗？

‍

好的，如果您有其他问题或需要帮助，请随时告诉我。祝您有个愉快的一天！

‍

‍

​![image](http://127.0.0.1:51365/assets/image-20241115174754-7ni8xtm.png)​
