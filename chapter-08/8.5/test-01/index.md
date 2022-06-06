本文实验需要准备两台机器。一台作为客户端，另一台作为服务器。如果你选用的是 c 或者 php 源码，这两台机器内存只要大于 4GB 就可以。 如果使用的是 Java 源码，内存要大于 6 GB。对 cpu 配置无要求，哪怕只有 1 个核都够用。

#### 调整客户端可用端口范围

默认情况下，Linux 只开启了 3 万多个可用端口。但我们今天的实验里，客户端一个进程要达到 5 万的并发。所以，端口范围的内核参数需要修改。

```sh
# vi /etc/sysctl.conf
net.ipv4.ip_local_port_range = 5000 65000
```

执行 sysctl -p 使之生效。



#### 调整客户端最大可打开文件数

我们要测试百万并发，所以客户端的系统级参数 fs.file-max 需要加大到 100 万。另外 Linux 上还会存在一些其它的进程要使用文件，所以我们需要多打一些余量出来，直接设置到 110 万。  

对于进程级参数 fs.nr_open 来说，因为我们开启 20 个进程来测，所以它设置到 60000 就够了。这些都在 /etc/sysctl.conf 中修改。

```sh
# vi /etc/sysctl.conf
fs.file-max=1100000 
fs.nr_open=60000  
```

sysctl -p 使得设置生效。并使用 sysctl -a 查看是否真正 work。

```sh
# sysctl -p
# sysctl -a
fs.file-max = 1100000
fs.nr_open = 60000
```

接着再加大用户进程的最大可打开文件数量限制（nofile）。这两个是用户进程级的，可以按不同的用户来区分配置。 这里为了简单，就直接配置成所有用户 * 了。每个进程最大开到 5 万个文件数就够了。同样预留一点余地，所以设置成 55000。 这些是在 /etc/security/limits.conf 文件中修改。  

> 注意 hard nofile 一定要比 fs.nr_open 要小，否则可能导致用户无法登陆。

```sh
# vi /etc/security/limits.conf
*  soft  nofile  55000  
*  hard  nofile  55000
```

配置完后，开个新控制台即可生效。 使用 ulimit 命令校验是否生效成功。

```sh
# ulimit -n
55000
```



####  服务器最大可打开文件句柄调整

服务器系统级参数 fs.file-max 也直接设置成 110 万。 另外由于这个方案中服务器是用单进程来接收客户端所有的连接的，所以进程级参数 fs.nr_open， 也一起改成 110 万。 

```sh
# vi /etc/sysctl.conf
fs.file-max=1100000 
fs.nr_open=1100000  
```

sysctl -p 使得设置生效。并使用 sysctl -a 验证是否真正生效。

接着再加大用户进程的最大可打开文件数量限制（nofile），也需要设置到 100 万以上。  

```sh
# vi /etc/security/limits.conf
*  soft  nofile  1010000  
*  hard  nofile  1010000
```

配置完后，开个新控制台即可生效。 使用 ulimit 命令校验是否成功生效。



#### 为客户端配置额外 20 个 IP

假设可用的 ip 分别是 CIP1，CIP2，......，CIP20，你也知道你的子网掩码。

> 注意：这 20 个 ip 必须不能和局域网的其它机器冲突，否则会影响这些机器的正常网络包的收发。

在客户端机器上下载的源码目录 test02 中，找到你喜欢用的语言，进入到目录中找到 tool.sh。修改该 shell 文件，把 IPS 和 NETMASK 都改成你真正要用的。

为了确保局域网内没有这些 ip，最好先执行代码中提供的一个小工具来验证一下

```sh
# make ping
```

当所有的 ip 的 ping 结果均为 false 时，进行下一步真正配置 ip 并启动网卡。

```sh
# make ifup
```

使用 ifconfig 命令查看 ip 是否配置成功。

```sh
# ifconfig
eth0
eth0:0
eth0:1
...
eth:19
```



#### 开始实验

ip 配置完成后，可以开始实验了。

在服务端中的 tool.sh 中可以设置服务器监听的端口，默认是 8090。启动 server

```sh
# make run-srv
```

使用 netstat 命令确保 server 监听成功。 

```sh
# netstat -nlt | grep 8090
tcp  0   0.0.0.0:8090  0.0.0.0:*  LISTEN
```

在客户端的 tool.sh 中设置好服务器的 ip 和端口。然后开始连接  

```sh
# make run-cli
```

同时，另启一个控制台。使用 watch 命令来实时观测 ESTABLISH 状态连接的数量。

**实验过程中不会一帆风顺，可能会有各种意外情况发生。** 这个实验我前前后后至少花了有一周时间，所以你也不要第一次不成功就气馁。  遇到问题根据错误提示看下是哪里不对。然后调整一下，重新做就是了。 重做的时候需要重启客户端和服务器。  

对于客户端，杀掉所有的客户端进程的方式是

```sh
# make stop-cli
```

对于服务器来说由于是单进程的，所以直接 ctrl + c 就可以终止服务器进程了。 如果重启发现端口被占用，那是因为操作系统还没有回收，等一会儿再启动 server 就行。 

当你发现连接数量超过 100 万的时候，你的实验就成功了。  

```sh
# watch "ss -ant | grep ESTABLISH"
1000013
```

这个时候别忘了查看一下你的服务端、客户端的内存开销。

先用 cat proc/meminfo 查看，重点看 slab 内存开销。  

```sh
$ cat /proc/meminfo
MemTotal:        3922956 kB
MemFree:           96652 kB
MemAvailable:       6448 kB
Buffers:           44396 kB
......
Slab:          3241244KB kB
```

再用 slabtop 查看一下内核都是分配了哪些内核对象，它们每个的大小各自是多少。

![](chapter-08/8.4/2_slabtop_light.png)

如果发现你的内核对象和上图不同，也不用惊慌。因为不同版本的 Linux 内核使用的内核对象名称和数量可能会有些许差异。


#### 结束实验

实验结束的时候，服务器进程直接 ctrl + c 取消运行就可以。客户端由于是多进程的，可能需要手工关闭一下。

```sh
# make stop-cli
```

最后记得取消为实验临时配置的新 ip  

```sh
# make ifdown
```

