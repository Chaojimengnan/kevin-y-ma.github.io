---
title: BreakWall 2 - V2ray
---

[参考](https://www.4spaces.org/digitalocean-build-v2ray-0-1/)以及[官方文档](https://www.v2ray.com/chapter_00/install.html)

## 1. 下载安装脚本

``` shell
wget https://install.direct/go.sh
```

## 2. 安装

```shell
chmod u+x ./go.sh
./go.sh #如果无法从GitHub下载软件，可以执行 ./go.sh -l ./v2ray-xxx-xx.zip从本地安装
```

> Linux服务端和客户端都通过此脚本进行安装，服务端和客户端的区别在于配置文件不同。
>
> Windows和macOS版本客户端都有GUI根据参考1进行配置，主要根据服务端填写 port\id\level\alterId几项。

## 3. 配置

配置文件路径：`/etc/v2ray/config.json`

服务端配置

```shell
{
  "inbounds": [{
    "port": ports, 					// 根据需要修改此处port
    "protocol": "vmess",
    "settings": {
      "clients": [
        {
          "id": "uuid", 		// 此处一般由服务器自动生成，需要复制粘贴进客户端
          "level": 1, 
          "alterId": 64
        }
      ]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}
```

客户端配置

```shell
{
  "inbounds": [{
    "port": 1080,  													// SOCKS 代理端口，在浏览器中需配置代理并指向这个端口
    "listen": "127.0.0.1",
    "protocol": "socks",
    "settings": {
      "udp": true
    }
  }],
  "outbounds": [{
    "protocol": "vmess",
    "settings": {
      "vnext": [{
        "address": "server_ip", 						// 服务器地址，请修改为你自己的服务器 ip 或域名
        "port": server_port, 	 							// 服务器端口
        "users": [{ "id": "server_uuid" }] 	// 复制服务器配置文件的UUID
      }]
    }
  },{
    "protocol": "freedom",
    "tag": "direct",
    "settings": {}
  }],
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules": [{
      "type": "field",
      "ip": ["geoip:private"],
      "outboundTag": "direct"
    }]
  }
}
```

## 4. 启动服务

``` shell
启动 ： service  v2ray start 或者 systemctl start v2ray
停止 ： service  v2ray stop 或者 systemctl stop v2ray
重启 ： service  v2ray restart 或者 systemctl restart v2ray
```

