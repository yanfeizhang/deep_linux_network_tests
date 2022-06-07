
### 第二次印刷基础之上的修改  
- P283 & P284 两页夹着的源码，正确的应该是  
```sh
# ip addr add 192.168.0.100/24 dev veth1_p
# ip netns exec net1 ip addr add 192.168.0.101/24 dev veth1
# ip link set dev veth1_p up 
# ip netns exec net1 ip link set dev veth1 up 
```
- P86 “缩微代码” => “微缩代码”
- P22，图2.9  两遍的序号都应该是 “0,1,2,3,4,5,...,512”
