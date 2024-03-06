---
title: 绿联NAS部署 frp 实现内网穿透
date: 2023-12-28T22:23:57+08:00
updated: 2023-12-28
tags:
  - nas 
  - docker
  - frp
---

买入[绿联NAS](../nas-setting/)后，再解锁docker技能后，玩法丰富了太多。部署在NAS的各种容器，为了能方便外网访问，在这个公网ip紧缺的时代，能实现内网穿透尤为重要。
而 [frp](https://github.com/fatedier/frp) 则是一个开源的常见解决方案。
> frp 是一款高性能的反向代理应用，专注于内网穿透。它支持多种协议，包括 TCP、UDP、HTTP、HTTPS 等，并且具备 P2P 通信功能。使用 frp，您可以安全、便捷地将内网服务暴露到公网，通过拥有公网 IP 的节点进行中转。

<!-- more -->

## 本质分析
通过公网服务器部署的frps，利用frpc把客户端的请求反向代理寻址访问到正确的服务地址，从而实现frps-frpc之间的流量转发。

## 部署服务端
### 下载frps
通过官方[release](https://github.com/fatedier/frp/releases/)下载服务器合适版本，如`frp_0.53.2_linux_amd64.tar.gz`，然后解压

### 创建服务端配置文件
```bash
nano ./frps.toml
```
比较简单实用的配置

```toml
# This configuration file is for reference only. Please do not use this configuration directly to run the program as it may have various issues.

# A literal address or host name for IPv6 must be enclosed
# in square brackets, as in "[::1]:80", "[ipv6-host]:http" or "[ipv6-host%zone]:80"
# For single "bindAddr" field, no need square brackets, like `bindAddr = "::"`.
bindAddr = "0.0.0.0" //服务器ip 默认这个就行
bindPort = 7000 //绑定端口




# Heartbeat configure, it's not recommended to modify the default value
# The default value of heartbeatTimeout is 90. Set negative value to disable it.
# transport.heartbeatTimeout = 90

# Pool count in each proxy will keep no more than maxPoolCount.
transport.maxPoolCount = 5




# transport.tls.force specifies whether to only accept TLS-encrypted connections. By default, the value is false.
tls.force = false


vhostHTTPPort = 8080 //多个虚拟主机访问端口


# Response header timeout(seconds) for vhost http server, default is 60s
# vhostHTTPTimeout = 60



# Configure the web server to enable the dashboard for frps.
# dashboard is available only if webServer.port is set.
webServer.addr = "127.0.0.1" //web管理url
webServer.port = 7500 //对应访问端口
webServer.user = "admin" //管理员用户名
webServer.password = "admin" //管理员密码


# Enable golang pprof handlers in dashboard listener.
# Dashboard port must be set first
webServer.pprofEnable = false

# enablePrometheus will export prometheus metrics on webServer in /metrics api.
enablePrometheus = true

# console or real logFile path like ./frps.log
log.to = "./frps.log"
# trace, debug, info, warn, error
log.level = "info"
log.maxDays = 3
# disable log colors when log.to is console, default is false
log.disablePrintColor = false

# DetailedErrorsToClient defines whether to send the specific error (with debug info) to frpc. By default, this value is true.
detailedErrorsToClient = true

# auth.method specifies what authentication method to use authenticate frpc with frps.
# If "token" is specified - token will be read into login message.
# If "oidc" is specified - OIDC (Open ID Connect) token will be issued using OIDC settings. By default, this value is "token".
auth.method = "token" //frps-frpc通讯验证方式

# auth token
auth.token = "12345678" //一串随机数，但要保证客户端也一样



# Max ports can be used for each client, default value is 0 means no limit
maxPortsPerClient = 0

# If subDomainHost is not empty, you can set subdomain when type is http or https in frpc's configure file
# When subdomain is est, the host used by routing is test.frps.com
subDomainHost = "frps.com" //多个虚拟主机(域名)的主域名
```
### 启动服务端
`nohub ./frps -c ./frps.toml &` 

可以用 `netstat -ntpl` 检查上述绑定的各种端口是否已经建立

## 部署客户端
### 创建客户端配置文件
提前把 `frpc.ini` 配置好，保存在NAS某个文件夹中，参考配置如下：

```ini
[common]

server_addr = x.x.x.x //公网vps地址
server_port = 7000 //之前服务端绑定的端口
token = 12345678 //与服务端必须一样，不然连不通

#log_file = /dev/null
#log_level = info
#log_max_days = 3
// 每创建一个proxy就建一个[ ]
[books]
type = tcp
local_ip = 192.168.3.226
local_port = 7070
remote_port = 5115 //tcp这个类别可以指定远程服务端端口

[bt]
type = http
local_ip = 192.168.3.226
local_port = 8080
subdomain = mybt

[jellyfin]
type = http
local_ip = 192.168.3.226
local_port = 11301
subdomain = movie
```
### 新建容器
在绿联的docker中搜索frpc镜像（如`stilleshan/frpc:latest`），拉取到本地后新建容器，记得存储空间要把`frpc.ini`路径挂载为`/frp/frpc.ini`
最后启动容器。查看日志，看是否连接服务端，通讯是否成功。

## 测试
访问 `vpsip:7500` 查看是否相关proxy都是online的，在线才证明成功连接上。

## ENJOY
尽情享受，访问  subdomain.frps.com:8080 即可获取NAS部署在内网的各种服务和容器。

- vpsip:5115 即是 192.168.3.226:7070 这个服务
- mybt.frps.com:8080 即是 192.168.3.226:8080 这个服务
- movie.frps.com:8080 即是 192.168.3.226:11301这个服务






