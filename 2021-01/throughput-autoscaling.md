# [throughput-autoscaling]

一句话梗概：主要是讲了根据qps去预测资源

#### 最重要的一张图

| 步骤 | 具体的数据 | 含义|
| ------ | ------ | -------|
| 1.Predict | 30 | 预测的qps |
| 2.Live metrics | 28 |实际的qps|
| 3.Combine | 30| 1和2结合|
| 4.Expected events | 40 | 已知的测试、特性引入|
| 5.Disaster buffer | 50 | 其他机房容灾|
| 6.Convert to machine count |  5|转化为机器数


[throughput-autoscaling]: <https://engineering.fb.com/2020/09/14/networking-traffic/throughput-autoscaling/>