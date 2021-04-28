### [Go应用的性能优化](https://xargin.com/go-perf-optimization/)

优化的前置知识:  
1. cpu
2. 内存
3. 磁盘
4. 网络  

keypoint： 优化越靠近应用层，效果越好。  

优化的工作流程：  
1. 建立指标
2. 发现问题
3. 解决问题
4. 回到2

可用的工具：
1. memory
2. cpu-profile:SIGPROF 信号。在 Go 进程中会选择随机的信号执行 sigtrampgo; on-cpu profile
3. fgprof:off-cpu profile; 在阻塞的情况；
4. trace
5. 系统层面的profile;即：perf。  


微观性能优化：
1. test
宏观性能优化
1. qps等；


一些优化案例：
1. gc mark 占用过多 CPU； objects太多；sync.pool
2. findrunnable: goroutine 太多导致；
3. 内存优化
4. 锁优化：横向："拆"-->大范围锁变小范围锁；纵向：缩-->锁的临界区。


[如何发现问题](https://xargin.com/continuous-profiling/)  

当波动率超过可以接受的范围，则认为当前进程发生了资源使用抖动，那么：
- 若 CPU 抖动，从当前开始采集 CPU profile 5秒
- 若 RSS 抖动，将当前进程的 heap profile 记录下来
- 若 goroutine 抖动，将当前进程的 goroutine profile 记录下来。