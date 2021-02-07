# [Understanding TCP/IP Network Stack & Writing Network Apps](https://www.cubrid.org/index.php?mid=blog&page=2&document_srl=3826497)   

一句话梗概：describe　data flow and control flow in Linux OS and the hardware layer　　　

|主题|小项|核心点|
|------|------|------|
|数据发送与接收|||
||数据发送|user-area,kernel-area,device-area|
||数据接收|tcp-checksum is computed by NIC|
|网络栈开发的时下热点|||
||包处理的过程干预|like：firewall,nat,traffic control.这块应该是bpf的内容|
||协议性能|提升:吞吐，延时，稳定性，例如多种拥塞算法优化|
||包处理的效率|pps,cpu/内存优化等，相关技术如：并行处理，header prediction,zero-copy,single-copy,checksum offload,TSO,LRO,RSS.|
|Control-Flow|||
||事件驱动|qdisc(发数据用)，NAPI(收数据用)|
||一些实际问题的点的优化|multi-queue NIC,RSS,RPS|
|关键的数据结构|||
||sk_buff(skb)|http://vger.kernel.org/~davem/skb.html 链表结构|
||TCP-Control-Block||
|驱动与网卡的交互方式|||
||主要结构|rx-tx-ring,interrupt.一些优化点：1.调大ring-buffer2.中断聚合|
|Stack Buffer and Flow Control|||
||send|App->[socket-buff]->SOCKET/TCP/IP->[qdisc]->Driver->[tx ring]->NIC->[internal-buff]->wire|
||recive|App<-[socket-buff]<-SOCKET/TCP/IP<-[NAPI(poll),no buff]<-Driver-[rx ring]<-NIC<-[internal buffer]<-wire|
||两个点|1.调大各种缓存   2.收Driver数据时用的NAPI|
|Conclusion|||
||要点|不需要去了解代码的每一行，知道上下文也是OK的|
||一些性能优化的名词|TSO, LRO, RSS, GSO, GRO, UFO, XPS, IOAT, DDIO, and TOE|

```go
NAPI:
As a compromise, the Linux kernel uses the interrupt-driven mode by default 
and only switches to polling mode when the flow of incoming packets 
exceeds a certain threshold, known as the "weight" of the network interface.

The kernel kicks off the "packet received" interrupt, which a network device
 driver using NAPI detects. The network device driver then disables interrupts 
 related to receiving packets and using NAPI, tells the Linux networking 
 subsystem to poll the device driver. The poll function is implemented by 
 the device driver, and is passed to the networking subsystem, and contains 
 the device driver's packet handler. After enough packets are received or
  a timeout is reached, packet-receiving interrupts are re-enabled, and 
  everything starts over again.

So NAPI is basically just a centralized API in the Linux networking subsystem 
for supporting interrupt coalescing to reduce receive livelock situations.
 NAPI gives device driver developers a clean framework for interrupt coalescing. 
 NAPI does not run all the time, but only happens when traffic is actually 
 received, making it essentially an interrupt coalescing scheme
```