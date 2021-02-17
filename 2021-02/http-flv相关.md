###  http-flv 
http-flv是一种协议，协议主题通俗来讲就是一堆不同类型的tag传输.  
tag主要有 I帧(关键帧)，P帧。I帧起播。两个I帧之间的数据就是gop(group of picture?)。  
秒开gop就要小，gop小压缩比旧大，对带宽要求就会高。  


现在整理下http-flv的传输。

正常的情况下，一条直播流为 tag1,tag2,tag3,tag4,进行传输。  
app端按照tag信息逐个去解析。  
过热度后，bkj一侧开始拉流，同时在视频关键帧后添加不影响正常逻辑的定位帧: index + offset.
tmp流变成了 tag3,tag3-,tag4,tag4-....
sdk将tmp流拿了过来，开始正常的解析，处理。这时候app开始走sdk的流，即单源模式。  
同时sdk侧通过bkj,skj来收piece,能解析出chunk，即多源模式。
chunk定长，因此通过offset可以知道从这个chunk的哪个偏移开始解析。  

```go
tag： |---|-------|----|------|----|---|------|
chunk:            |----|----|----|----|----|----|----|
```
所以说引入了单源模式，只是为了把offset拿过来，这样的话chunk就能找到分界线了。

去单源模式的话，chunk内增加了tag偏移。(解析中加入了跳chunk,因为可能chunk内不足一个帧。)
这样的话，sdk收到chunk流之后，解出tag,往外扔就行了。

因此说：app层始终是用md5做拼接。进入单源。有单源时，使用了offset做了拼接。  
```go
offset实际起到了两个作用：
1. 单源与多源的数据拼接。
2. 标记chunk内tag的位置。
```

去单源之后，不用拼接数据，chunk内的tag起始位置自我标识，就不用offset了。

ps： 这块chunkid与offset的转换还是有点问题：
chunkid = (index*(11+4)+offset)/chunksize. chunksize=32*864-16.