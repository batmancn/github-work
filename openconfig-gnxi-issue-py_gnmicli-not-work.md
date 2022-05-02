https://github.com/google/gnxi/issues/91

从上面的讨论看到可能是TLS问题

解决方案讨论在这里：https://ones.sankuai.com/ones/product/3958/workItem/defect/detail/5725594

【问题定位】：

【可能原因1】：

server端（也就是MNOS telemetry），使用的gRPC库是"google.golang.org/grpc"

client端，如果使用go版本的gnmi_get，使用的gRPC库是"google.golang.org/grpc"，与server端通信正常

抓包通过wireshark解析，显示通信过程全部是TCP报文

client端，如果使用python版本的gnmi_get，使用的gRPC库是"grpc"，与server端通信异常

抓包通过wireshark解析，显示通信过程中，client发出的是GRPC报文，server端回复确认TCP层，但是上层无回复

初步定位应该是client端与server端使用的gRPC库不同导致的问题。

【可能原因2】：

server端（也就是MNOS telemetry），使用'-insecure'模式（仅用于测试），也就是使用testcert，客户端连接进来后从服务器端获取这个testcert，再通信。

go版的客户端，支持这个流程，python版的不支持。

这个问题有两种改法，一是python版模仿go版，开发这样的功能。这样的好处是server端不需要做修改，继续使用现有模式。

二是客户端和server端，都使用TLS证书启动，这样好处是安全，但是问题是现有已经部署的怎么搞，另外大规模线上部署的时候，每台设备都搞证书？

GRPC之间通信，有几种模式
1. notls模式，明文
2. tls模式，使用cert、key，加密
3. insecure模式，用于test的，server端使用tls.go，根据本地时间、example.com等生成临时证书，client端从远端获取

目前telemetry支持3种
go客户端gnmi_get支持2、3
python客户端支持1、2