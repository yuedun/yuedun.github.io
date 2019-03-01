gRPC是谷歌开发的跨语言（C, C++, Python, PHP, Nodejs, C#, Objective-C、Golang、Java）RPC框架，跨语言是指可以使用gRPC进行个语言之间的通信，例如：PHP可以对java进行远程调用。

在系统架构中，我们会把多个系统公共的模块拆分出来做成单独的服务，可以提供RESTful接口，也可以为了低延迟快速响应而提供RPC接口。如果选择了gRPC，上线后发现多个系统都请求这个RPC服务提供者，而且流量很大的时候负载过高导致崩溃。为了降低负载和提高可用性，理所当然的要做集群，用nginx作为代理服务器。那么请看以下配置即可：

```
upstream grpcservice {
    server localhost:50051;
    server localhost:50052;
}
server {
    listen       8080  http2;#需要加http2
    server_name  localhost;

    location / {
        grpc_pass grpc://grpcservice;#以grpc为前缀
    }

    grpc_connect_timeout 10;
}
```
配置好nginx以后，客户端需要连接到localhost:8080来调用远程服务。
效果图：![grpc-nginx](http://hopefully-img.yuedun.wang/grpc_nginx.gif "grpc-nginx")
可以看到，每一次请求两个RPC服务器都有输出，证明请求被分配到了两台服务器上。

虽然我们使用了两台服务器来保证性能和可用性，但是当其中一台服务器挂掉以后发现部分请求响应非常慢。
![grpc_nginx2](http://hopefully-img.yuedun.wang/grpc_nginx2.gif "grpc_nginx2")
原因是服务器虽然宕机，但是请求还会发送到挂掉的服务器上，然后等待超时（默认1分钟），超时后再请求另外的服务器，重新请求以后可能还会再次分配到这台宕机的服务器。为了能加快响应，配置了`grpc_connect_timeout`选项，把时间设为5秒，再次测试，大概5秒后就能返回。如果设置更小的时间响应时间会更短。
![grpc_nginx3](http://hopefully-img.yuedun.wang/grpc_nginx3.gif "grpc_nginx3")