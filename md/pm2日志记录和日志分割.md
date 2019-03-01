# pm2日志记录和日志分割

## pm2介绍
pm2是nodejs进程管理工具，现在基本是node生产服务器的标准选择，可以帮助我们实现node多进程服务，开启的多个实例自动实现负载均衡。
最重要的是保证node单进程不会因为错误退出，作为守护进程保证nodejs服务不宕机。
总体来说就是有性能监控、自动重启、负载均衡的作用。

## pm2-logrotate介绍
pm2本身是可以输出日志文件的，默认的文件路径：
> error log path    │ /home/username/.pm2/logs/app-error-0.log
> out log path      │ /home/username/.pm2/logs/app-out-0.log 

但是pm2的日志文件不能自动分割，这会导致一个文件不断变大，不但影响性能，查看这些日志也会带来麻烦。所以需要`pm2-logrotate`来实现自动分割日志。
## 安装pm2-logrotate
`pm2 install pm2-logrotate`,是用pm2命令，不是npm命令

pm2-logrotate配置

+ ``max_size` (Defaults to 10M): When a file size becomes higher than this value it will rotate it (its possible that the worker check the file after it actually pass the limit) . You can specify the unit at then end: 10G, 10M, 10K
+ ``retain` (Defaults to all): This number is the number of rotated logs that are keep at any one time, it means that if you have retain = 7 you will have at most 7 rotated logs and your current one.
+ `compress` (Defaults to false): Enable compression via gzip for all rotated logs
+ `dateFormat` (Defaults to YYYY-MM-DD_HH-mm-ss) : Format of the data used the name the file of log
+ `rotateModule` (Defaults to true) : Rotate the log of pm2's module like other apps
+ `workerInterval` (Defaults to 30 in secs) : You can control at which interval the worker is checking the log's size (minimum is 1)
+ `rotateInterval` (Defaults to 0 0 * * * everyday at midnight): This cron is used to a force rotate when executed. We are using node-schedule to schedule cron, so all valid cron for node-schedule is valid cron for this option. Cron style :
+ `TZ` (Defaults to system time): This is the standard tz database timezone used to offset the log file saved. For instance, a value of Etc/GMT-1, with an hourly log, will save a file at hour 14 GMT with hour 13 GMT-1 in the log name.

```
*    *    *    *    *    *
┬    ┬    ┬    ┬    ┬    ┬
│    │    │    │    │    |
│    │    │    │    │    └ day of week (0 - 7) (0 or 7 is Sun)
│    │    │    │    └───── month (1 - 12)
│    │    │    └────────── day of month (1 - 31)
│    │    └─────────────── hour (0 - 23)
│    └──────────────────── minute (0 - 59)
└───────────────────────── second (0 - 59, OPTIONAL)
```

可以尝试使用`pm2 set pm2-logrotate:max_size 1K (1KB)`设置日志文件最大为1KB
代码中`console.log`的内容会输出到`/home/username/.pm2/logs/app-out-0.log`,大小达到1KB就会自动生成`app-out-0__2018-01-25_16-58-16.log`这种格式的日志文件。
`/home/username/.pm2/logs/`（默认路径，可以配置）路径下还有
+ `app-error-0.log`
+ `pm2-logrotate-out-1.log`
+ `pm2-logrotate-error-1.log`
几个文件，`app-out-0.log`文件记录的就是`console.log`输出，`app-error-0.log`记录的是错误输出，`pm2-logrotate-out-1.log`和`pm2-logrotate-error-1.log`分别记录的是分割的日志文件，是这样的内容：
```
"/home/username/.pm2/pm2__2018-01-25_16-57-16.log" has been created
"/home/username/.pm2/logs/app-out-0__2018-01-25_16-58-16.log" has been created
"/home/username/.pm2/logs/app-out-0__2018-01-25_16-58-46.log" has been created
```
