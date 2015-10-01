---
layout: post
title:  "Linux网络虚拟化的一些概念"
categories:   linux
date:   2014-11-07
---

##bridge
Bridge（桥）是 Linux 上用来做 TCP/IP 二层协议交换的设备，与现实世界中的交换机功能相似。Bridge 设备实例可以和 Linux 上其他网络设备实例连接，既 attach 一个从设备，类似于在现实世界中的交换机和一个用户终端之间连接一根网线。当有数据到达时，Bridge 会根据报文中的 MAC 

##TAP 设备与 VETH 设备
当一个 TAP 设备被创建时，在 Linux 设备文件目录下将会生成一个对应 char 设备，用户程序可以像打开普通文件一样打开这个文件进行读写。当执行 write()操作时，数据进入 TAP 设备，此时对于 Linux 网络层来说，相当于 TAP 设备收到了一包数据，请求内核接受它，如同普通的物理网卡从外界收到一包数据一样，不同的是其实数据来自 Linux 上的一个用户程序。Linux 收到此数据后将根据网络配置进行后续处理，从而完成了用户程序向 Linux 内核网络层注入数据的功能。当用户程序执行 read()请求时，相当于向内核查询 TAP 设备上是否有需要被发送出去的数据，有的话取出到用户程序里，完成 TAP 设备的发送数据功能。针对 TAP 设备的一个形象的比喻是：使用 TAP 设备的应用程序相当于另外一台计算机，TAP 设备是本机的一个网卡，他们之间相互连接。应用程序通过 read()/write()操作，和本机网络核心进行通讯。

VETH 设备总是成对出现，送到一端请求发送的数据总是从另一端以请求接受的形式出现。该设备不能被用户程序直接操作，但使用起来比较简单。创建并配置正确后，向其一端输入数据，VETH 会改变数据的方向并将其送入内核网络核心，完成数据的注入。在另一端能读到此数据。

##netns
netns是在linux中提供网络虚拟化的一个项目，使用netns网络空间虚拟化可以在本地虚拟化出多个网络环境，目前netns在lxc容器中被用来为容器提供网络。

使用netns创建的网络空间独立于当前系统的网络空间，其中的网络设备以及iptables规则等都是独立的，就好像进入了另外一个网络一样。

##让两个netns下的网络通信
先介绍几个命令：
```
	ip netns add net0  #创建netns net0
	ip netns list		#列出所有netns
	ip netns exec net0 bash #进入net0
```
现在开始:

![network](/img/virtual_network.png)

创建虚拟网络并连接网线
```
	ip netns add net0
	ip netns add net1
	ip netns add bridge
	ip link add type veth
	ip link set dev veth0 name net0-bridge netns net0
	ip link set dev veth1 name bridge-net0 netns bridge
	ip link add type veth
	ip link set dev veth0 name net1-bridge netns net1
	ip link set dev veth1 name bridge-net1 netns bridge
```

在bridge中创建并且设置br设备
```
	ip netns exec bridge brctl addbr br
	ip netns exec bridge ip link set dev br up
	ip netns exec bridge ip link set dev bridge-net0 up
	ip netns exec bridge ip link set dev bridge-net1 up
	ip netns exec bridge brctl addif br bridge-net0
	ip netns exec bridge brctl addif br bridge-net1
```
然后配置两个虚拟环境的网卡
```
	ip netns exec net0 ip link set dev net0-bridge up
	ip netns exec net0 ip address add 10.0.1.1/24 dev net0-bridge
	ip netns exec net1 ip link set dev net1-bridge up
	ip netns exec net1 ip address add 10.0.1.2/24 dev net1-bridge
```

参考:

<a href="http://www.ibm.com/developerworks/cn/linux/1310_xiawc_networkdevice/">Linux 上的基础网络设备详解</a>

<a href="http://www.2cto.com/os/201312/266765.html">Linux 上虚拟网络与真实网络的映射</a>

<a href="http://www.360doc.com/content/13/1010/14/8504707_320319174.shtml">网络虚拟化技术（一）: Linux网络虚拟化
</a>


