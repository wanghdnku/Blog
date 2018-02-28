---
layout: post
title: 越过长城，走向世界
subtitle: 使用 Shadowsocks 科学上网
date: 2018-02-09 03:30:38
author: "Hayden Wang"
header-img: great_wall.jpg
cdn: header-on
tags: [Networks]
---

> **Across the Great Wall we can reach every corner in the world.**

## 1. SOCK5 and ShadowSocks

## 2. Server 部分

### 2.1 购买境外 VPS 并远程登录

- linode
- Dgital Ocean
- 

### 2.2 安装 Shadowsocks

> 说明：以下命令均在最高权限用户 root 下运行。

Debian / Ubuntu 用户运行：
```shell
 apt-get install python-pip
 pip install shadowsocks
```

CentOS 用户运行：
```shell
yum install python-setuptools && easy_install pip
pip install shadowsocks
```

### 2.3 优化 Shadowsocks 性能（可选）

按照 SS 官方 Wiki，有如下优化策略：

1. 创建 local.conf 配置文件：`vim /etc/sysctl.d/local.conf`

2. 在配置文件中输入以下内容并保存：
```yaml
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla

# for low-latency network, use cubic instead
# net.ipv4.tcp_congestion_control = cubic
```

3. 使配置生效：`sysctl --system`

### 2.4 配置 Shadowsocks 配置文件

建议把 SS 的配置文件放置在当前用户主目录下的 ss 文件夹内，对于 root 用户而言，则是：`/root/ss` 目录。其余用户一般则是：`/home/用户名` 目录。

下面我们以 root 用户为例：

1. 在 root 文件夹内，新建 ss/ssserver.json 配置文件：`vim ~/ss/ssserver.json`

2. 编辑配置文件，依然是按 i 进入编辑，按 ESC 退出编辑，按 :wq 保存并退出：
```json
{
    "server": "my_server_ip", // 这里输入本机的 IP 地址
    "server_port": 8388, // 为了安全，可修改为大于 1024 的数字
    "local_address": "127.0.0.1",
    "local_port": 1080, // 为了安全，可修改为大于 1024 的数字
    "password": "mypassword", // 设置一个密码
    "timeout": 300,
    "method": "aes-256-cfb",
    "fast_open": false
}
```

### 2.5 启动并永久运行 Shadowsocks 服务端

一句命令即可启动并永久运行：
```shell
nohup ssserver -c /root/ss/ssserver.json -d start &
```

> 说明： ssserver 是 SS 的服务端命令。-c 表示以配置文件的方式运行 SS，/root/ss/ssserver.json 则是步骤 4 中新建的配置文件的路径。-d 表示在后台运行，这样允许用户进行其他操作。start 就是启动。nohup 以及最后的 & 是让 SS 服务端一直运行，并把运行日志输出到当前用户主目录下的 nohup.out 文件中。

停止 SS 服务端：
```shell
ssserver -c /root/ss/ssserver.json -d stop
```

> 说明： 无需 nohup 和 &，把 start 换成 stop


### 2.6 使用 BBR 加速（可选）

---

## 3. Client 部分

### 3.1 Linux 端解决方案

```shell
sslocal -s server_ip -p server_port -l 1080 -k password -t 600 -m aes-256-cfb
```

- -s 表示服务IP
- -p 指的是服务端的端口
- -l 是本地端口默认是1080（可以替换成任何端口号，只需保证端口不冲突）
- -k 是密码（要加""）
- -t 超时默认300
- -m是加密方法默认aes-256-cfb

> 可以将以上命令写入一个sh文件中，以后每次运行一下脚本就可以

### 3.2 MacOS 端解决方案

### 3.3 Windows 端解决方案

### 3.4 iOS 端解决方案

### 3.5 Android 端解决方案


## References

- https://www.loyalsoldier.me/fuck-the-gfw-with-my-own-shadowsocks-server/