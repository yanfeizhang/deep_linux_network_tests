# 一、准备工作
为了方便观察，需要准备两台服务器。一台作为客户端、另一台作为服务器。

## 客户端准备

为了本实验的顺利进行，客户端需要进行一些内核参数的调整。

- 调整 ip_local_port_range 来 保证可用端口数要大于 5 万。
- 保证 tw_reuse 和 tw_recycle 是关闭状态的，否则连接无法进入 TIME_WAIT
- 调整 tcp_max_tw_buckets 保证能有 5 万个 TIME_WAIT 状态供观察。

```sh
# vi /etc/sysctl.conf
net.ipv4.ip_local_port_range = 5000 65000
net.ipv4.tcp_tw_reuse = 0
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_max_tw_buckets = 60000

# sysctl -p

# cat /proc/meminfo
Slab:              39848 kB
...
```

## 服务端准备

一般我们测试的时候本地的两台机器的 RTT 都很短，零点几毫秒，很容易把连接队列打满，进而导致握手过慢。
为了避免这个问题，所以需要确认或修改一下系统 somaxconn 的大小（源代码中的 backlog 都设置的是 1024，但必须要 somaxconn 大于这个数字才能生效）。

```sh
# vi /etc/sysctl.conf
net.core.somaxconn = 1024
# sysctl -p

# cat /proc/meminfo
Slab:              53512 kB
...
```

先分别记录一下服务器和客户端实验开始前的 slab 内存开销，例如。

```sh
# cat /proc/meminfo
...
```

# 二、测试开始
在服务器机器上，进入 test-02 目录，再选择一门你熟悉的语言。无论选择哪门语言，下面的操作过程描述都是通用的。

启动 Server  

```sh
# make run-srv
```

再到另外的客户端机器上，首先修改 Makefile 中的服务器 IP（默认是 192.168.0.1 ）。然后启动客户端

```sh
# make run-cli
```

然后客户端开始顺序建立连接。

# 三、观察结果

## 1. 观察客户端内存开销

先确认一下连接总数

```sh
# netstat -antp | grep ESTABLISH | wc -l
50017
```

再查看内存开销

```sh
# slabtop
...
# cat /proc/meminfo
...
```

## 2. 观察服务端内存开销

```sh
# slabtop
...
# cat /proc/meminfo
...
```

