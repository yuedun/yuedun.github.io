# 在docker中运行nodejs

首先看项目目录：
![项目目录](http://hopefully.qiniudn.com/projectdir.png)

再看Dockerfile文件内容：
```
# 以最新的node为基础镜像
FROM hub.c.163.com/library/node:latest
# 工作目录为app
WORKDIR /app
# 拷贝当前所在项目根目录到app目录
COPY . /app
# 全局安装pm2
RUN npm install pm2 -g

EXPOSE 8081
#使用pm2启动nodejs，如果没有--no-daemon参数docker启动后就退出
CMD ["pm2-runtime", "dest/server.js", "--no-daemon"]
# ENTRYPOINT ["node", "server.js"]
```

或者在Dockerfile中不添加CMD命令，可以在启动docker时执行命令：
`docker run --name ks -ti -p 8081:8081 kser:pm2 pm2-runtime dest/server.js`

如果是后台运行的docker，
`docker run --name ks -d -p 8081:8081 kser:pm2 pm2-runtime dest/server.js`
`-d`选项是后台运行

需要进入到docker查看pm2运行情况
，可以通过`docker exec -ti ks /bin/sh`查看运行的容器内部情况
![pm2运行情况](http://hopefully.qiniudn.com/pm2running.png)