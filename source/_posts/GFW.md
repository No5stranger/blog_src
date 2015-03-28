title: hello world, goodbye GFW.
date: 2015-03-28 19:31:10
tags: hack_teck
---
最近天朝越来越封闭了，各种被墙，Github也偶尔来凑凑热闹，抽下风。实在忍受不了这种被虐的感觉。翻墙搞起！
一台国外VPS， [shadowsocks](https://github.com/shadowsocks/shadowsocks), 轻松廉价搭起翻墙代理。

### VPS选择
关于VPS选择，[wable](https://www.linode.com)价格实惠，$6/mo，而且可以开两个VPS，3TB流量，足够平时的翻墙了。使用一段时间发现wable有个坑，镜像虽然有多个选择，但是其实Linux内核只停留在2.6，而且无法升级内核，所以打算顺便折腾下服务器，玩下docker什么的慎重考虑。
[Linode](https://www.linode.com)性能高，节点多，$10/mo, 但流量只有1TB。[vultr](https://www.vultr.com)介于wable和Linodes之间，$7/mo, 2TB流量，还是不错的，性价比高。

### Do not like VPS?
如果不想折腾VPS？直接购买一个shadowsocks账号吧，很多这种服务网站。

### shadowsocks 安装
#### Debian / Ubuntu:
```
apt-get install python-pip
pip install shadowsocks
```
#### CentOS:
```
yum install python-setuptools && easy_install pip
pip install shadowsocks
```
服务器端和客户端安装是一样的， `ssserver` 和 `sslocale` 会同时装上。

### 本地启动shadowsocks
#### 配置本地shadows
新建配置文件`/path/to/shadowsocks.json`：
配置官方介绍：https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File
```
{
    "server":"my_server_ip",  //服务器地址
    "server_port":8388, //服务器shadowsocks服务监听端口
    "local_port":1085, //本地监听端口
    "password":"mypassword", //与服务期配置文件密码一致
    "timeout":300,
    "method":"aes-256-cfb",
}
```
#### 启动shadowsocks
```
sslocal -c /path/to/shadowsocks.json
```

### 看看长城外的风景
Chrome配合goagent，配置SOCKETS Host和Port即可翻墙。 不推荐使用全局代理。

### 配置git代理(GIT协议/SSH协议/HTTP协议)
使用[connect](https://bitbucket.org/gotoh/connect)进行代理转换
#### GIT协议配置
##### 建立/usr/bin/socks5proxywrapper
```
#!/bin/sh
connect -S 127.0.0.1:1085 "$@"
```
#### SSH协议配置
##### 建立/usr/bin/socks5proxyssh
```
#!/bin/sh
ssh -o ProxyCommand="/usr/bin/socks5proxywrapper %h %p" "$@"
```
##### 配置git使用代理
```
export GIT_SSH="/usr/bin/socks5proxyssh“"
```
#### 全局配置git
##### git配置文件(~/.gitconfig)添加以下内容:
```
[core]
    gitproxy = /usr/bin/socks5proxywrapper
[http]
    proxy = socks5://127.0.0.1:1085
[https]
    proxy = socks5://127.0.0.1:1085
[url "https://"]
    insteadOf = git://
```

这样git即可使用shadowsocks代理了，无论是使用何种协议。
