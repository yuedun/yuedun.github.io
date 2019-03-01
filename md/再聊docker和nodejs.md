# 再聊docker和nodejs

上一篇写到了[如何在docker中运行nodejs](http://yuedun.wang/blogdetail/5a6021fa4bdc8ea471fd2db0)，运行方式是在docker中安装了pm2来保证node服务宕机重启，这种方式更像是把docker当做虚拟机来使用。其实，既然使用了docker的话就可以不使用pm2来管理进程，因为docker自身可以充当守护进程，在node进程退出时进行重启。只要在启动docker容器时加上--restart=always参数即可。例如：`docker run -d --restart=always -p 3000:3000 mynode:1`

## 没有pm2如何开启多进程
使用pm2可以开启多node进程，并且自带负载均衡，但是有个限制，pm2可以开启的进程数是CPU最大核心数。而使用docker的话就不会受限于此了，开启几十个上百个node服务都可以，然后通过nginx实现负载均衡。不过要手动开启几十上百个docker容器那怎么行？让我手动开启3个都很烦了，这时候就需要用到docker编排工具了，比如：`Docker Swarm、Kubernetes、docker compose`等，可以一键开启多个容器。