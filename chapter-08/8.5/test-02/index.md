#### 调整可用端口范围

同方案一，客户端机端口范围的内核参数也是需要修改的。

```sh
# vi /etc/sysctl.conf
net.ipv4.ip_local_port_range = 5000 65000
```

执行 sysctl -p 使之生效。



#### 客户端加大最大可打开文件数

同方案一，客户端的 fs.file-max 也需要加大到 110 万。进程级的参数 fs.nr_open 设置到 60000。

```sh
# vi /etc/sysctl.conf
fs.file-max=1100000 
fs.nr_open=60000  
```

sysctl -p 使得设置生效。并使用 sysctl -a 查看是否真正生效

客户端的 nofile 设置成 55000  

```sh
# vi /etc/security/limits.conf
*  soft  nofile  55000  
*  hard  nofile  55000
```

配置完后，开个新控制台即可生效。



#### 服务器最大可打开文件句柄调整

同方案一，调整服务器最大可打开文件数。不过方案二服务端是分了 20 个进程，所以 fs.nr_open 改成 60000 就足够了。

```sh
# vi /etc/sysctl.conf
fs.file-max=1100000 
fs.nr_open=60000  
```

sysctl -p 使得设置生效。并使用 sysctl -a 验证。

接着再加大用户进程的最大可打开文件数量限制（nofile），这个也是 55000。  

```sh
# vi /etc/security/limits.conf
*  soft  nofile  55000  
*  hard  nofile  55000
```

> 再次提醒： hard nofile 一定要比 fs.nr_open 要小，否则可能导致用户无法登陆。

配置完后，开个新控制台即可生效。



#### 开始实验

启动 server

```sh
# make run-srv 
```

使用 netstat 命令确保 server 监听成功。 

```sh
# netstat -nlt | grep 8090
tcp  0  0  0.0.0.0:8100  0.0.0.0:*  LISTEN
tcp  0  0  0.0.0.0:8101  0.0.0.0:*  LISTEN
......
tcp  0  0  0.0.0.0:8119  0.0.0.0:*  LISTEN
```

回到客户端机器，修改 tool.sh 中的服务器 ip。端口会自动从 tool.sh 中加载。然后开始连接

```sh
# make run-cli
```

同时，另启一个控制台。使用 watch 命令来实时观测 ESTABLISH 状态连接的数量。

期间如果做失败了，需要重新开始的话，那需要先杀掉所有的进程。在客户端执行 `make stop-cli`，在服务器端是执行 `make stop-srv`。重新执行上述步骤。

当你发现连接数量超过 100 万的时候，你的实验就成功了。  

```sh
# watch "ss -ant | grep ESTABLISH"
1000013
```

记得查看同样一下你的服务端、客户端的内存开销。

```sh
# cat /etc/redhat-release
Red Hat Enterprise Linux Server release 6.2 (Santiago)

# ss -ant | grep ESTAB |wc -l
1000013

# cat /proc/meminfo
MemTotal:        3925408 kB
MemFree:           97748 kB
Buffers:           35412 kB
Cached:           119600 kB
......
Slab:            3241528 kB
```

再用 slabtop 查看一下 top 内核对象。


实验结束的时候，记得`make stop-cli`结束所有客户端进程，`make stop-srv`结束所有服务器进程。

