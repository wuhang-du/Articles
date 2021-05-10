### [bfe开源框架](https://github.com/baidu/bfe-book)  

- bfe是什么
    - 七层负载均衡接入转发平台，主要还是网络接入。
    - bfe在[整体架构中所处的位置](https://github.com/baidu/bfe-book/blob/version1/design/gslb/gslb.md),本质上已经是dns调度之后的东西。其实还有个lvs.
- 核心模块
    - 转发引擎：提供流量转发功能
    - 配置中心：配置管理
    - 监控：webmonitor/日志
- [负载均衡核心逻辑](https://github.com/baidu/bfe-book/blob/version1/implementation/balancing/balancing.md)
    - 第一层：子集群： 全局负载均衡(GSLB) BFE集群利用全量的用户流量、后端容量、网络情况，在多个后端子集群之间实现负载均衡。
        - 例如：20:30:50的三个集群，0-20,20-50,50-100；
    - 第二层：集群内的服务实例： 基于随机顺序的加权随机：
        - 第一步：打乱同一个集群的实例；
        - 第二步：wrr算法，具体如下:
  
例如: 假设后端子集群包含三个后端实例a/b/c，权重分别为 5/1/1。如果基于以上算法，选择的过程如下：

| 轮数 | 选择前偏好指数 | 选中节点 | 选择后偏好指数 |
| ---- | -------------- | -------- | -------------- |
| 1    | 5  1  1        | a        | 3  2  2        |
| 2    | 3  2  2        | a        | 1  3  3        |
| 3    | 1  3  3        | b        | 6 -3  4        |
| 4    | 6 -3  4        | a        | 4 -2  5        |
| 5    | 4 -2  5        | c        | 9 -1 -1        |
| 6    | 9 -1 -1        | a        | 7  0  0        |
| 7    | 7  0  0        | a        | 5  1  1        |