### 第一章 Primer on Latency and Bandwidth


### 第二章 Building Blocks of TCP


### 第三章 Building Blocks of UDP

```go
如何探测一个client的nat类型

步骤如下：
1、client按照stun协议，发送udp包。server发送回外网的cip1与cport1.
2、如果收不到回包:
        则说明udp被限制，即：udp-block。nat：0。结束流程。
   否则，收到回包：
            如果 ip1,port1与client自身的ip,port相同：
                则说明不存在nat，public类型.nat：1，结束流程。
            否则进入步骤3.
3、client发送探测包，要求server使用ip2,port2进行返回。
4、如果收到回包：
        则说明是 full-clone。 nat: 2。 结束流程。
    否则，进入步骤5.
5、client向ip2,port2发送探测包，期望返回外网cip2,cport2.
6、判断cip1,cport1与cip2,cport2的关系，一般ip应该相同，看port.
    如果port不相同：
        则说明是 syem 对称性。nat：5.
    否则进入步骤7.
7、client向ip2,port2发送探测包，要求以ip2,port3返回。
    如果能收到回包：
        则说明是ip限制型， nat： 3。
    否则是
        端口限制型，nat： 4。
```

一言以蔽之，nat映射是指内网中的一个ip,port组合映射到外网，以及与外网ip,port连接限制的关系。  
  
假设内网中的设备为ip1,port1.  
|nat类型|含义|映射出来的ip，port|连接限制|
|-|-|-|-|
|1|public|稳定：ip1:port1|直连|
|2|full-clone|稳定：ip2:port2|任意外界的ip,port都可以连接|
|3|ip-res|稳定：ip2:port2|ip限制型，必须是设备向外发包，但是发包之后，外界的ip一样，端口不一样可以连接。|
|4|port-res|稳定：ip2:port2|port限制型，必须是设备向外发包，发包之后，只有确定ip,port才能连接上。|
|5|syem|变化：ip2:portn|任意ip:port都生成了不同的ip:port对。即这种设备就不能主动被连接。|
  
不同nat直接的连接关系。被动方的内网port是不变的，待连接的。
|横列为主动方 |nat0|1|2|3|4|5|补充说明|
|-|-|-|-|-|-|-|-|
|nat0|×|×|×|×|×|×||
|nat1|×|√|√|√|√|√||
|nat2|×|√|√|√|√|√||
|nat3|×|√|√|√|√|√|5连3，感觉是稳定的。|
|nat4|×|√|√|√|√|×|nat5想和nat4连接： 5与mona通信，告诉mona自身意图与nat4相连。但是它真正映射出来的ip,port没人知道。|
|nat5|×|√|√|√|×|×|nat4想和nat5连接：问题在于 5收到mona指令，向外发包，的映射出来的ip,port没人知道。3-5：要求被连方先向主动方的ip发包，把通路先打开，然后只能是5向3发包，才能连上。|  

### 第四章 Transport Layer Security 

安全传输是在基本的传输层之上再加上加解密这一层。

```go
    client ------------------------ server
    1.syn-send --->
    2.                         <-----ack,syn
    3.ack -->                                

    下面一堆..... 得到一个对称加密的秘钥。

    4.data----->
    5.                          <-----data
```

关于如何得到秘钥的几个问题：
1. 如何证明你是你？client也不能和任意一个阿猫阿狗通信。
    引入ca证书。server端会将自己的公钥带着，同时将ca的签名带着。
    ca的签名是一个ca的私钥加密之后的，ca的公钥是内置在浏览器中的。
2. client搞一个密码，使用server传的公钥加密。这样server能看到，开始通信吧。
    隐患：迟早被破解，因此引入了一个 Diffie-Hellman key exchange，不会公开传秘钥。
3. 好像还有一个校验功能。  

因此来说：tls提供了识别，加密，防篡改 等功能。
还有一些具体的流程上的优化。

[一个关于ca证书的链接](https://www.cnblogs.com/franson-2016/p/5530671.html)