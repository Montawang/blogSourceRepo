---
title: '[SSR] ShadowsocksR 配置及使用'
mathjax: true
copyright: true
comment: true
date: 2018-11-26 14:54:29
tags: 
    - ShadowsocksR
categories:
    - Crypto
photo:
---

{% note default %}

有同学问我如何使用 ShadowsocksR 来加密以保证自己的网络安全，这个口头讲起来真的是挺烦的，所以这里就写一篇教程。
**本篇说明仅供交流与学习使用，请勿作出任何违反国家法律的行为！**

{% endnote %}

<!-- more -->

### 何为 ShadowsocksR

想要知道 `ShadowsocksR` 为何物，首先要知道 `Shadowsocks` 是什么。它是既可以指一种基于 `Socks5` 代理方式的加密传输协议，也可以指实现这个协议的各种开发包。目前包使用 `Python`、`C`、`C++`、`C#`、`Go` 等编程语言开发，大部分主要实现（`iOS` 平台的除外）采用 `Apache` 许可证、`GPL`、`MIT` 许可证等多种自由软件许可协议开放源代码。`Shadowsocks` 分为服务器端和客户端，在使用之前，需要先将服务器端部署到服务器上面，然后通过客户端连接并创建本地代理。`ShadowsocksR` 是 `breakwa11` 发起的 `Shadowsocks` 分支，在 `Shadowsocks` 的基础上增加了一些数据混淆方式，称修复了部分安全问题并可以提高 `QoS` 优先级。后来贡献者 `Librehat` 也为 `Shadowsocks` 补上了一些此类特性，甚至增加了类似 `Tor` 的可插拔传输层功能。

### 安装 ShadowsocksR

#### 客户端下载

1. [Windows](https://github.com/shadowsocksrr/shadowsocksr-csharp/releases)
2. [macOS](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Shadowsocks-for-OSX-Help)
3. [Linux](https://github.com/librehat/shadowsocks-qt5)
4. [Android](https://github.com/shadowsocks/shadowsocks-android)
5. [iOS](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Help)

#### 配置服务端 ShadowsocksR

1. 首先你要有一个 VPS，推荐使用 Vultr 这个 VPS 服务商
2. 使用 ssh 登陆到 root 用户后运行一下命令（这是一位大佬的一键配置文件，[他的网站](https://teddysun.com/)）：
```shell
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
chmod +x shadowsocksR.sh
./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```
3. 接下来设置密码端口，加密方式，协议，混淆（obfs）就可以了。
4. 安装完成后，会有脚本提示如下：
```
Congratulations, ShadowsocksR server install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Protocol         :your_protocol
Your obfs             :your_obfs
Your Encryption Method:your_encryption_method

Welcome to visit:https://shadowsocks.be/9.html
Enjoy it!
```
5. 将你的配置信息输入到客户端中就可以使用了

### 管理你的 ShadowsocksR

#### 卸载

```shell
./shadowsocksR.sh uninstall
```

#### 查看状态

```shell
/etc/init.d/shadowsocks status
```

#### 启动

```shell
/etc/init.d/shadowsocks start
```

#### 停止

```shell
/etc/init.d/shadowsocks stop
```

#### 重启

```shell
/etc/init.d/shadowsocks restart
```

#### 文件目录

1. 配置文件路径：`/etc/shadowsocks.json`
2. 日志文件路径：`/var/log/shadowsocks.log`
3. 代码安装目录：`/usr/local/shadowsocks`

#### 配置多用户

示例：

```json
{
  "server":"0.0.0.0",
  "server_ipv6": "[::]",
  "local_address":"127.0.0.1",
  "local_port":1080,
  "port_password":{
    "8989":"password1",
    "8990":"password2",
    "8991":"password3"
  },
  "timeout":300,
  "method":"aes-256-cfb",
  "protocol": "origin",
  "protocol_param": "",
  "obfs": "plain",
  "obfs_param": "",
  "redirect": "",
  "dns_ipv6": false,
  "fast_open": false,
  "workers": 1
}
```

其中把 `"server"` 替换成自己的地址就可以了，`"port_password"` 自己设置，其它的视自己情况更改。修改配置文件参考：
1. https://github.com/shadowsocksr-backup/shadowsocks-rss/wiki/Server-Setup
2. https://github.com/shadowsocksr-backup/shadowsocks-rss/blob/master/ssr.md
3. https://github.com/shadowsocksr-backup/shadowsocks-rss/wiki/config.json

---

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-layout="in-article"
     data-ad-format="fluid"
     data-ad-client="ca-pub-7465666912424994"
     data-ad-slot="3198608984"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

<br/>
