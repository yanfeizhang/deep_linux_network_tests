
### 第二次印刷基础之上的勘误与优化 
- P14: 图2.3 上面这段话中函数调用关系描述反了，整段话改成如下这样: "系统初始化的时候会执行到 spawn_ksoftirqd（位于kernel/softirq.c）来创建出 softirqd 进程，执行过程如图 2.3。"
- P15: 图2.4 中第4步 "soft_irq_vec" 修改为 "softirq_vec"
- P16: 图2.5 “fs_initcall(fs_initcall)” => “fs_initcall(inet_init)”
- P19: 图2.6 下下端开头的描述有误，net_device_ops 应该为 igb_netdev_ops。整段话修改后就是“第 6 步注册net_device_ops用的是igb_netdev_ops变量，其中包含了igb_open，该函数在网卡被启动的时候会被调用。”
- P20: 启动网卡小节， 驱动向内核注册了xxx，这里的 “structure net_device_ops” 应该改成 “struct net_device_ops”
- P22: 此页开头的代码描述和样式有问题，整段代码改成如下形式。
```c
//file: drivers/net/ethernet/intel/igb/igb_main.c
int igb_setup_rx_resources(struct igb_ring *tx_ring)
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
- P33: 很多同学都反馈咱们书中竖着的流程图特别的棒，比如P122，强烈要求在第二章里也加上。其实咱们有这个图，只不过前面没有用。把图 5.5 数据接收源码的图片在 2.2.4 小节这里加一下，提升用户体验。加的位置放到这一句话后面“当数据到来以后，第一个迎接它的是网卡：”。不过要注意的是这样本章后面的图片的序号就都得变一下了。
- P34: "而skb会随着收包过程而动态申请"，这句话有一点点不严谨，修改成“而skb虽然也会预分配好，但是在后面收包过程中会不断动态地分配申请”
- P45: “首先调用sock_alloc来分配一个struct sock内核对象”，这里的 “sock” 应该改为 “socket”
- P86: “缩微代码” => “微缩代码”
- P92: 图4.5 两边的序号都应该是 “0,1,2,3,4,5,...,512”，而且还不应该有顿号“、”
- P254: 9.1 中的建议1的第三段， 其中有一个 UDP 错写成 UPD 了。
- P283 & P284: 两页夹着的源码，正确的应该是  
```sh
# ip addr add 192.168.0.100/24 dev veth1_p
# ip netns exec net1 ip addr add 192.168.0.101/24 dev veth1
# ip link set dev veth1_p up 
# ip netns exec net1 ip link set dev veth1 up 
```


### 致谢
感谢@巩鹏军、@彭东林、@孙国路、@王锦、@随行、@harrytc、@t涛、@point 等同学提出的非常棒的改进建议！

