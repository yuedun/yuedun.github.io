# 本地连接阿里云ECS自建mongodb
启动mongodb服务
> /usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data --logpath=/usr/local/mongodb/logs --logappend 

```
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server. 
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] **          Start the server with --bind_ip <address> to specify which IP 
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
2018-06-06T16:00:03.250+0800 I CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.
```
启动以后想在本地通过可视化工具连接
