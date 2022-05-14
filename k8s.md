# 我的开源之路

# 1.入门

- 初步看了各种文档
- 接口理论?
- 测试入门

```go

type TestContext struct {
	CloseFn            framework.CloseFunc
	HTTPServer         *httptest.Server 
	NS                 *v1.Namespace  // todo: check
	ClientSet          clientset.Interface
	KubeConfig         *restclient.Config
	InformerFactory    informers.SharedInformerFactory
	DynInformerFactory dynamicinformer.DynamicSharedInformerFactory
	Scheduler          *scheduler.Scheduler
	Ctx                context.Context
	CancelFn           context.CancelFunc
}
```
- apiServer
```go
type APIServerHolder struct {
	Initialized chan struct{}
	M           *controlplane.Instance
}
```
- scheduler 

http-server: 上层的接口
    -api-reciver
        - api-server
