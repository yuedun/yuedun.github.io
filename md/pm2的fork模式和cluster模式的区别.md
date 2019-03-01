pm2的fork模式和cluster模式的区别

## fork模式
pm2默认启动的是fork模式，是以单核单进程运行的，在fork模式下可以直接运行coffee-script，PHP，python。
参考：[http://pm2.keymetrics.io/docs/tutorials/using-transpilers-with-pm2](http://pm2.keymetrics.io/docs/tutorials/using-transpilers-with-pm2)

cluster模式可以根据CPU数量进行实例扩展，可以开启多进程而不需要修改代码。可提高程序性能和可靠性。类似于分布式系统，只不过是在单台机器上开启多实例，而pm2自带负载均衡。
![](http://i.imgur.com/kTAowsL.png)

** 使用方法 **
`pm2 start app.js -i max`
使用-i参数，max代表最大CPU进程数

也可以使用文件方式：
processes.json文件：
```json
{
  "apps" : [{
    "script"    : "api.js",
    "instances" : "max",
    "exec_mode" : "cluster" 
  }]
}
```
`pm2 start processes.json`