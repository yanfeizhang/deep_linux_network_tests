

准备工作

```sh
# make create-net1
# make create-br
# make create-nat
```


访问外网

```sh
# ip netns exec net1 ping 10.153.55.119
PING 10.153.55.119 (10.153.55.119) 56(84) bytes of data.
64 bytes from 10.153.55.119: icmp_seq=1 ttl=57 time=2.12 ms
64 bytes from 10.153.55.119: icmp_seq=2 ttl=57 time=1.76 ms
```

监听请求

```
# ip netns exec net1 nc -lp 80
```

在另外一台机器上 ping 这台机器

```c
# telnet 10.162.132.110 8088
Trying 10.162.132.110...
Connected to 10.162.132.110.
Escape character is '^]'.
......
```


