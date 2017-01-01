# iptables-VPN-ss-redir
# iptable 转发 VPN 到 ss-redir

### 待解决

- chinadns 配置，dns 转发
- 忽略内网流量后、是否影像 vpn 流量转发

### 安装 shadowsocks-libev 版本：

	apt-get install libssl-dev
	git clone https://github.com/shadowsocks/shadowsocks-libev.git
	cd shadowsocks-libev
	./configure
	make
	make install

### 用法

	ss-[local | redir]

	启动脚本

	#!/bin/sh
	ss-local -c /root/ss/local.json -f /root/ss/local.pid
	ss-redir -c /root/ss/redir.json -f /root/ss/redir.pid


##### ss 配置

	# ss-local
	{
	    "server":"SERVERIP",
	    "server_port":443,
	    "local_address": "10.0.0.200",
	    "local_port":1080,
		"password": "PASSWORD",
		"method": "aes-256-cfb",
		"timeout": 60
	}

	# ss-redir
	{
	    "server":"SERVERIP",
	    "server_port":443,
	    "local_address": "10.0.0.200",
	    "local_port":1080,
		"password": "PASSWORD",
		"method": "aes-256-cfb",
		"timeout": 60
	}


### iptables 配置

***添加顺序不能错**

	# 新建一个 chian
	iptables -t nat -N SHADOWSOCKS

	# 忽略 ssserver 的 IP， 123.123.123.123 表示服务器IP
	iptables -t nat -A SHADOWSOCKS -d 123.123.123.123 -j RETURN

	# 忽略内网流量
	iptables -t nat -A SHADOWSOCKS -d 0.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 10.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 127.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 169.254.0.0/16 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 172.16.0.0/12 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 192.168.0.0/16 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 224.0.0.0/4 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 240.0.0.0/4 -j RETURN

	＃ 转发所有 nat 表 chian SHADOWSOCKS 的流量
	iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT --to-ports 1080

	# 将 nat 表 chian PREROUTING 转发到 chian SHADOWSOCKS
	iptables -t nat -A PREROUTING -p tcp -j SHADOWSOCKS


### 现在用的 iptables 规则 ( updated 20160128 )

	#!/bin/sh

	#create a new chain named SHADOWSOCKS
	iptables -t nat -N SHADOWSOCKS

	# Ignore your shadowsocks server's addresses
	# It's very IMPORTANT, just be careful.
	iptables -t nat -A SHADOWSOCKS -d 45.55.27.136 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 174.140.166.38 -j RETURN

	# Ignore LANs IP address
	iptables -t nat -A SHADOWSOCKS -d 0.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 10.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 127.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 169.254.0.0/16 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 172.16.0.0/12 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 192.168.0.0/16 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 224.0.0.0/4 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 240.0.0.0/4 -j RETURN

	# Ignore Asia IP address
	iptables -t nat -A SHADOWSOCKS -d 1.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 14.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 27.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 36.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 39.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 42.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 49.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 58.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 59.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 60.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 61.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 101.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 103.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 106.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 110.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 111.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 112.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 113.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 114.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 115.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 116.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 117.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 118.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 119.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 120.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 121.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 122.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 123.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 124.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 125.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 126.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 169.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 175.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 180.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 182.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 183.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 202.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 203.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 210.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 211.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 218.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 219.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 220.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 221.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 222.0.0.0/8 -j RETURN
	iptables -t nat -A SHADOWSOCKS -d 223.0.0.0/8 -j RETURN

	# Anything else should be redirected to shadowsocks's local port
	iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT --to-ports 1080

	# Apply the rules
	iptables -t nat -A PREROUTING -p tcp -j SHADOWSOCKS


### 参考

- [https://github.com/shadowsocks/shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)
- [https://v2ex.com/t/230624](https://v2ex.com/t/230624)（v2ex 讨论帖子）
- [分享一个用树莓派+SS+ChinaDNS+bandwagon 做网关的帖子](http://blog.csdn.net/lostgdi/article/details/42643645)
- [用树莓派2代打造智能家庭路由](http://hugozhu.myalert.info/2015/02/26/54-raspberrypi2-pptp-redsocks2-chinadns.html)
- [关于iptables mangle表的作用,请指教.](http://bbs.chinaunix.net/thread-2139019-1-1.html)
- [搭建智能翻墙路由器](http://hbprotoss.github.io/posts/da-jian-zhi-neng-fan-qiang-lu-you-qi.html)
- [iptables 添加，删除，查看，修改](http://blog.51yip.com/linux/1404.html)
- [树莓派搭建透明代理](http://liberize.me/tech/raspberry-pi-transparent-proxy.html)
