---
layout: post
title: 我的树莓派食谱（二）
subtitle: 远程控制树莓派
author: Hayden Wang
header-img: respberry_1.jpg
cdn: header-on
tags: [Raspberry Pi]
date: 2018-02-22 16:34:56
---

## 1. 用 SSH 远程控制树莓派

SSH 是连接远程计算机的一种常见方法，只要是树莓派本身支持的命令，都可以在 SSH 中使用。同时，SSH 也是一种安全的连接方式，因为通信是加密的。这种方法唯一的缺点，可能在于它的运行环境是命令行，而非图形环境。

### 1.1 在树莓派中开启 SSH

开启树莓派的 SSH 服务很简单，只需要在终端中打开配置：

```shell
sudo raspi-config
```

然后选择 Interface Option, Enable SSH 即可。

![Config](/images/raspberrypi/config.png)

> 对于较新版本的 Raspbian 来说，SSH 已经自动启用了，所以根本无需修改任何设置。

### 1.2 在终端远程登录树莓派

从一台安装了 MacOS 或 Linux 的计算机上面连接树莓派，只需要打开 终端窗口，键入下列命令即可：

```shell
ssh pi@192.168.1.115 
# 或者 ssh 192.168.1.115 -l pi
```

这里的 IP 地址（192.168.1.16）是树莓派的内网 IP 地址。然后输入密码就可以登录到树莓派了。

![SSH Example](/images/raspberrypi/ssh.png)

> 如果从 Windows 远程连接树莓派的话，可以使用 Putty 来启动一个 SSH 会话。

### 1.3 使用 SFTP 管理树莓派上的文件

SFTP 全称 SSH File Transfer Protocol，为 SSH 的其中一部分，是一种传输文件至服务器的安全方式。其实在 SSH 软件包中，已经包含了一个 SFTP 安全文件信息传输子系统，SFTP 本身没有单独的守护进程。和 SSH 一样，SFTP 同样是使用加密传输认证信息和传输的数据，所以，使用 SFTP 可以认为安全的。但是，由于这种传输方式使用了加密/解密技术，所以传输效率比普通的 FTP 要低。

要用 SFTP 访问树莓派上的文件，只需要安装 [FileZilla](https://www.filezilla.cn/download/client) 客户端，输入树莓派的地址，用户名，密码，端口号（SSH 默认端口是 22）即可。

![FileZilla](/images/raspberrypi/filezilla.png)

---
<br>


## 2. 用 VNC 远程控制树莓派

除了使用 SSH 命令行登录树莓派，还可以通过 VNC 远程访问树莓派的图形界面。

### 2.1 在树莓派上安装 VNC 服务器

在树莓派上打开终端会话（或 SSH 会话），然后运行下列命令。
```shell
sudo apt-get update
sudo apt-get install tightvncserver
```

安装好 VNC 服务器之后，请使用下列命令来运行它。
```shell
vncserver :1
```
第一次运行时，会要求建立密码，也就是说，今后任何远程连接树莓派的人，必须输入该密码后才能获得访问权限。

### 2.2 远程登录树莓派图形界面

为了从远程计算机连接树莓派，需要安装 VNC 客户端。[RealVNC](http://www.realvnc.com) 是一个不错的选择，可以很好地与 tightvnc 建立连接， 并且支持 Windows、Linux、MacOS 甚至 iOS 系统。

当运行客户端程序的时候，它会要求输入你想连接的 VNC 服务器的 IP 地址，也就是你的树莓派的 IP 地址。可以在 IP 地址后面输入“:1”，用以表示你希望连接的桌面号为 1。之后，会要求输入密码。

> 注意：这是指安装 tightvncserver 后设置的那个密码，并不要求与树莓派密码一致。

现在，就可以顺利地看到树莓派的桌面了：

![VNC Viewer](/images/raspberrypi/vnc_viewer.png)

### 2.3 让树莓派每次开机自动启动 VNC 服务

如果你希望树莓派每次重启都会自动启动 VNC 服务器的话，可以借助下列命令来实现。
```shell
cd /home/pi
cd .config
mkdir autostart
cd autostart
vim tightvnc.desktop
```

将下列内容粘贴到文件中，并 :wq 保存：
```
[Desktop Entry]
Type=Application
Name=TightVNC
Exec=vncserver :1
StartupNotify=false
```
树莓派一旦设置为自动登录并进入桌面环境的话，VNC 服务器就会在重新启动后自动启动。


---
<br>


## 3. 在 MacOS 的 Finder 中访问树莓派

只需要对树莓派做一些简单的配置，就能让树莓派出现在 MacOS 的 Finder 列表中，从而可以使用 Finder 来管理其文件系统。

### 3.1 安装并配置 netatalk

首先，在树莓派上输入下列命令来安装 netatalk。

```shell
sudo apt-get install netatalk
```

对于树莓派来说，还有一些配置需要修改。

```shell
sudo apt-get install avahi-daemon
sudo update-rc.d avahi-daemon defaults
```

然后，输入下列命令。

```shell
sudo vim /etc/avahi/services/afpd.service
```

将下列内容粘贴到该文件中并保存。

```xml
<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
    <name replace-wildcards="yes">%h</name>
    <service>
        <type>_afpovertcp._tcp</type>
        <port>548</port>
    </service>
</service-group>
```

为了让它作为守护进程运行，可以使用如下所示的命令：

```shell
sudo /etc/init.d/avahi-daemon restart
```

### 3.2 在 Finder 中登录 AFP 服务

回到 Mac 上， 在 Finder 菜单中选择 Go→Connect to Server， 然后输入 afp://192.168.1.16 作为服务器地址（你要使用自己树莓派的 IP 地址替换这里的地址）。 然后，单击 Connect，系统就会提示登录。使用树莓派默认的用户 pi 和相应密码登录后，Finder 就会显示树莓派 home 目录下面的内容。

![AFP](/images/raspberrypi/afp.png)

## 4. 在外网中访问树莓派

由于 NAT 的存在，一般个人设备所使用的 IP 并不能在公网中通用，NAT 会将公网地址转换为内网地址，并建立一个映射。因此，想要在外网中直接访问树莓派，通过内网地址是不行的，必须想办法穿透 NAT。

### 4.1 DMZ 主机

待续

### 4.2 使用路由器的端口映射

待续

### 4.3 使用花生壳的服务

待续

### 4.4 配合 DDNS 服务，优雅地访问树莓派

现在我们所使用的上网服务，IP 地址会在上网时由运营商分配，这样以来，每次访问树莓派就要重新查询目前路由器所分配的外网地址，十分麻烦。还好现在的路由器一般都会支持 DDNS 服务。

