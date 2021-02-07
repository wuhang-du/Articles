# RTC相关技术(Real-Time Communication)    
[ali-GRTN]    
重点总结：  
1. 混合组网：树状层级结构+对等图形网
2. 能力下沉：协议边缘卸载+内部传输协议归一化
3. 控制和数据分离：动态路径规划+全分布式SFU  

架构升级所带来的核心价值是：  
1. 降成本，业务复用率高，另外 GRTN内部链路更短，节点内的成本也更低。
2. 提质量，内部组网采用动态SFU的方式来构建的网状结构，并且内部链路采用了私有协议来进行高效传输, 网络编解码自适应。
3. 易扩展，webrtc.


[华为-rtc]  
重点总结：  
1. 智能编解码+网络自适应双轮驱动  
2. 语言增强技术+丢包补偿


总结：  
+ 网络自适应的编解码，qos优化
+ 动态调度SFU

[华为-rtc]: <https://mp.weixin.qq.com/s/YQjHqAYlZTwwIdIhRB5Cfg>
[ali-GRTN]: <https://developer.aliyun.com/article/778264?utm_content=g_1000205299&comefrom=https://blogread.cn/news/>