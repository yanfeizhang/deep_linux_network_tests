### 第一次印刷基础之上的勘误和优化
- 封面：作者简介, “西北大学计算机学院” 改成 “西北大学信息科学与技术学院”
- P23 图 2.10  “4.调节驱动注册的硬中断处理函数”中的“调节”改成“调用”
- P26 "只要硬中断在哪个CPU上响应，那么软中断也是在这个CPU上处理的" 这一句加粗。
- P43 3.2 最下面一句，“我们来翻翻源码，看看上面的结构是如何被创造出来的。”这句话改成“我们来翻翻源码，看看看看图3.1中所示的结构是如何被创造出来的。”
- P44 图3.1 图中右上 “struct socket” => “struct sock”
- P45 第一行代码中的注释，“//调用每个协议族的...”改成“//调用指定协议族的...”
- P45 代码中开头 "static" 少了 个 s, 成了 “tatic” 了，另外这几行代码应该是对齐的。可能是tab和空格样式的在咱们两边不一致，所以印刷的时候错乱了。
- P47，倒数第5行的recvform，应是recvfrom
- P49 "回忆图3.1中的socket对象图" 这一句可以删了，后面有3.6就行了
- P49 同上，本业下方还有“从图3.1中得出”，这句改成“从图3.6中得出”
- P51 尾部 void 单独占一行怪怪的，把它和下一行合并起来把
- P52 3.3.2 "前文中讲到了"改成“第2章中”
- P67 最底下的代码对齐有点错乱。将 ep_ptable_queue_proc 函数中 `f (epi->nwait >= 0 && (pwq = kmem_cache_alloc(pwq_cache, GFP_KERNEL)))` 改成 `if (epi->nwait >= 0 && (pwq = kmem_cache_alloc(pwq_cache, GFP_KERNEL)))`
- P83: 最后面 Redis 6.0 版本中也开始支持“多进程”了改成“多线程”。
- P85: 根据错误图4中所示，将 3) 发送网络数据的时候 这个问题中的第二段，挪到 4.1 小节最后吧，更通顺一些。
- P129: 本页最后的一个 “个” 字删掉吧
- P185：“7 建立一条TCP连接需要消耗多长时间？” 在这一段中，“这个时间要比CPU本地的系统调用耗时短的多”，应该是“长的多”。
- P220最下面的代码 + P221 最上面的代码，这里的注释对齐有点问题，参考错误图5进行对齐一下吧
- P277最下面的代码缩进貌似有问题
- P303:第一段代码缩进有问题

### 第二次印刷基础之上的勘误与优化 
- P14: 图2.3 上面这段话中函数调用关系描述反了，整段话改成如下这样: "系统初始化的时候会执行到 spawn_ksoftirqd（位于kernel/softirq.c）来创建出 softirqd 线程，执行过程如图 2.3。"
- P15: 图2.4 中第4步 "soft_irq_vec" 修改为 "softirq_vec"
- P16: 图2.5 “fs_initcall(fs_initcall)” => “fs_initcall(inet_init)”
- P19: 图2.6 下下端开头的描述有误，net_device_ops 应该为 igb_netdev_ops。整段话修改后就是“第 6 步注册net_device_ops用的是igb_netdev_ops变量，其中包含了igb_open，该函数在网卡被启动的时候会被调用。”
- P20: 启动网卡小节， 驱动向内核注册了xxx，这里的 “structure net_device_ops” 应该改成 “struct net_device_ops”
- P22: 此页开头的代码描述和样式有问题，整段代码改成如下形式。
```c
//file: drivers/net/ethernet/intel/igb/igb_main.c
int igb_setup_rx_resources(struct igb_ring *rx_ring)
{
	//1. 申请 igb_rx_buffer 数组内存
	size = sizeof(struct igb_rx_buffer) * rx_ring->count;
	rx_ring->rx_buffer_info = vzalloc(size);
  
	//2. 申请 e1000_adv_rx_desc DMA 数组内存
	rx_ring->size = rx_ring->count * sizeof(union e1000_adv_rx_desc);
	rx_ring->size = ALIGN(rx_ring->size, 4096);
	rx_ring->desc = dma_alloc_coherent(dev, rx_ring->size,
					   &rx_ring->dma, GFP_KERNEL);
  
	//3.初始化队列成员
	rx_ring->next_to_alloc = 0;
	rx_ring->next_to_clean = 0;
	rx_ring->next_to_use = 0;

	return 0;
}
```
- P22: 图2.9  两边的序号都应该是 “0,1,2,3,4,5,...,512”
- P23: 图2.10 第四步的箭头画反了，应该是 CPU 指向驱动
- P24: "list_add_tail修改了CPU变量softnet_data"，改成 "list_add_tail修改了Per-CPU变量softnet_data"，这样更加明确
- P27: 源码处的源码结束位置有点小问题，把最后的 net_rx_action去掉，加一行 “...”。最终的源码应该为
```c
//file:net/core/dev.c
static void net_rx_action(struct softirq_action *h)
{
    struct softnet_data *sd = &__get_cpu_var(softnet_data);
    unsigned long time_limit = jiffies + 2;
    int budget = netdev_budget;
    void *have;

    local_irq_disable();

    while (!list_empty(&sd->poll_list)) {
        ......
        n = list_first_entry(&sd->poll_list, struct napi_struct, poll_list);

        work = 0;
        if (test_bit(NAPI_STATE_SCHED, &n->state)) {
            work = n->poll(n, weight);
            trace_napi_poll(n);
        }

        budget -= work;
        ...
    }
}
```
- P27:"一进来就调用local_irq_disable把所有的硬中断都关了",这句话不太严谨，应该改成"一进来就调用local_irq_disable把当前cpu的硬中断关了"
- P28: 这里的 igb_clean_rx_irq 代码在简写的时候把最重要的 while 给丢了
```c
//file: drivers/net/ethernet/intel/igb/igb_main.c
static bool igb_clean_rx_irq(struct igb_q_vector *q_vector, const int budget){
	...
	do {
		/* retrieve a buffer from the ring */
		skb = igb_fetch_rx_buffer(rx_ring, rx_desc, skb);

		/* fetch next buffer in frame if non-eop */
		if (igb_is_non_eop(rx_ring, rx_desc))
			continue;
		}

		/* verify the packet layout is correct */
		if (igb_cleanup_headers(rx_ring, rx_desc, skb)) {
			skb = NULL;
			continue;
		}

		/* populate checksum, timestamp, VLAN, and protocol */
		igb_process_skb_fields(rx_ring, rx_desc, skb);

		napi_gro_receive(&q_vector->napi, skb);
}
```
修改为
```c
//file: drivers/net/ethernet/intel/igb/igb_main.c
static bool igb_clean_rx_irq(struct igb_q_vector *q_vector, const int budget){
	...
	do {
		/* retrieve a buffer from the ring */
		skb = igb_fetch_rx_buffer(rx_ring, rx_desc, skb);

		/* fetch next buffer in frame if non-eop */
		if (igb_is_non_eop(rx_ring, rx_desc))
			continue;
		}

		/* verify the packet layout is correct */
		if (igb_cleanup_headers(rx_ring, rx_desc, skb)) {
			skb = NULL;
			continue;
		}

		/* populate checksum, timestamp, VLAN, and protocol */
		igb_process_skb_fields(rx_ring, rx_desc, skb);

		napi_gro_receive(&q_vector->napi, skb);
		...
	} while (likely(total_packets < budget));    
}
```
- P28: 有一个 skb 错打成 sbk 了，在 “开始设置sbk变量的” 这一句中，要把这个 sbk 改成 skb。
- P31: grep -r "NF_HOOK" * 这一块的代码样式调整一下更好看，修改成如下
```sh
# grep -r "NF_HOOK" *
net/ipv4/arp.c:	NF_HOOK(NFPROTO_ARP, NF_ARP_OUT, skb, NULL, skb->dev, dev_queue_xmit);
net/ipv4/arp.c:	return NF_HOOK(NFPROTO_ARP, NF_ARP_IN, skb, dev, NULL, arp_process);
net/ipv4/ip_input.c: return NF_HOOK(NFPROTO_IPV4, NF_INET_LOCAL_IN, skb, skb->dev, NULL,
net/ipv4/ip_input.c: return NF_HOOK(NFPROTO_IPV4, NF_INET_PRE_ROUTING, skb, dev, NULL,
net/ipv4/ip_forward.c: return NF_HOOK(NFPROTO_IPV4, NF_INET_FORWARD, skb, skb->dev,
net/ipv4/xfrm4_output.c: return NF_HOOK_COND(NFPROTO_IPV4, NF_INET_POST_ROUTING, skb,
net/ipv4/ip_output.c: NF_HOOK(NFPROTO_IPV4, NF_INET_POST_ROUTING,
net/ipv4/ip_output.c: NF_HOOK(NFPROTO_IPV4, NF_INET_POST_ROUTING, newskb,
net/ipv4/ip_output.c: return NF_HOOK_COND(NFPROTO_IPV4, NF_INET_POST_ROUTING, skb, NULL,
net/ipv4/ip_output.c: return NF_HOOK_COND(NFPROTO_IPV4, NF_INET_POST_ROUTING, skb, NULL,
......
```
- P33: 很多同学都反馈咱们书中竖着的流程图特别的棒，比如P122，建议在第二章里也加上。由于出版后页数不能变动，所以这里先简单改一下描述。等将来再版的时候，再把这个图加到第二章里来。将本页倒数第二行“当数据到来以后，第一个迎接它的是网卡：” ==> “当数据到来以后，第一个迎接它的是网卡，然后是硬中断、软中断、协议栈等环节的处理，参考图5.5的流程图。” 

- P34: "而skb会随着收包过程而动态申请"，这句话有一点点不严谨，修改成“而skb虽然也会预分配好，但是在后面收包过程中会不断动态地分配申请”
- P35: “将传过来的poll_list添加到了CPU变量softnet_data的poll_list里”，建议将“CPU变量”改为“Per-CPU变量”
- P45: “首先调用sock_alloc来分配一个struct sock内核对象”，这里的 “sock” 应该改为 “socket”
- P60: 图3.14中，将结构体struct eventpoll误写成struct eventepoll
- P63: 图3.16中，将结构体struct eventpoll误写成struct eventepoll
- P66 & P72: 图3.18 和图3.21 中的 struct file 这个方框中的 *socket 是错的，应该和图3.17一样，改为"*private_data"。
- P80: 在 1) 阻塞到底是怎么一回事中的"TASK_RUNNKNG" 改为 "TASK_RUNNING"
- P82: 本页中间的“等待时间”改成“等待事件”
- P86: “缩微代码” => “微缩代码”
- P92: 图4.5 两边的序号都应该是 “0,1,2,3,4,5,...,512”，而且还不应该有顿号“、”
- P116, 图4.22中，邻居子系统和网络层写的是相同的函数，邻居子系统应该是neigh_resolve_output
- P131 不小心把自己的内网ip露出了，将相应的代码改成
```sh
#telnet 10.162.*.* 8888
Trying 10.162.*.*...
telnet: connect to address 10.162.*.*: Connection refused
```
- P170: 自旋锁是一种非阻塞的锁的说法不太严谨，"是一种非阻塞的锁"这一句改成“是一种非睡眠锁”
- P232: 图8.4.1中的第一个消息气泡小了
- P254: 9.1 中的建议1的第三段， 其中有一个 UDP 错写成 UPD 了。
- P283 & P284: 两页夹着的源码，正确的应该是  
```sh
# ip addr add 192.168.0.100/24 dev veth1_p
# ip netns exec net1 ip addr add 192.168.0.101/24 dev veth1
# ip link set dev veth1_p up 
# ip netns exec net1 ip link set dev veth1 up 
```

### 第三次印刷基础之上的勘误和优化
- P12：图2.1下方的代码文件 driver/net/ethernet中的 “driver”改成“drivers”
- P22: 此页开头的代码函数中的参数有问题，修改下参数部分。
```c
int igb_setup_rx_resources(struct igb_ring *tx_ring)
```

修改为
```c
int igb_setup_rx_resources(struct igb_ring *rx_ring)
```
- P25，图2.11，驱动 lgb_poll()，这里应该是 igb_poll()
- P27: 目的是保证网络包的接收不霸占CPU不放。这里“不霸占CPU不放”修改成“不一直霸占CPU”，这样更通顺一些。
- P67 代码中的“if”少了个“i”。将 ep_ptable_queue_proc 函数中 
```c
f (epi->nwait >= 0 && (pwq = kmem_cache_alloc(pwq_cache, GFP_KERNEL)))
``` 
改成 
```c
if (epi->nwait >= 0 && (pwq = kmem_cache_alloc(pwq_cache, GFP_KERNEL)))
```
- P92: 图4.5发送队列细节中的数组应该是tx不是rx。所以“igb_rx_buffer”应该改成“igb_tx_buffer”。"e1000_adv_rx_desc" 应该改为 “e1000_adv_tx_desc”。
- P106: 图4.17中dev_hard_start_xmit应该是画在网络设备子系统里更合适。还有P109页的第二段里，“并也最终调用到驱动程序里的入口函数dev_hard_start_xmit”，这句话应该改成“并也最终调用到通往驱动程序的dev_hard_start_xmit”
- P190: 图7.3 NUMA中的node，图片中的“内存控置器”应该为“内存控制器”
- P193: 图7.6伙伴系统中的三个 “RELCLAIMABLE” 中多了个L，应该改为“RECLAIMABLE”
- P210: 第二段中间的，“这个功能会可能会...”，应该去掉一个会，改成“这个功能可能会...”
- P275: 第一行代码和P274页最底下的代码重了，所以把295页第一行“ip link add veth0 type veth peer name veth1”删掉
- P311: 页中有一句话，其他的路由规则，一般都是在main路由表中记录着的，可以用ip route list table local命令查看。这里的 “local”是错的，应该是“main”
- P312: 在发送数据的时候这一段中。后面有一句话，在这两个函数中分别过了OUTPUT和PREROUTING的各种规则。这里”PREROUTING”改成“POSTROUTING



### 致谢
感谢@巩鹏军、@彭东林、@孙国路、@王锦、@随行、@harrytc、@t涛、@point、@LJ、@WannaCry、@久、@jame 等同学提出的非常棒的改进建议！

