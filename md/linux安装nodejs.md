首先下载nodejs压缩文件node-v4.4.4-linux-x64.tar.xz，先解压文件，
`> xz -d node-v4.4.4-linux-x64.tar.xz`解压为tar格式文件，再通过tar命令解压
`> tar -xvf node-v4.4.4-linux-x64.tar`
可以将解压后的文件夹移动到自己的软件安装文件夹下，比如：`/usr/local`下，因为nodejs的版本更新太快，每次更新都这么移动文件夹太麻烦，所以还是改成一个通用的名字，就叫node，执行命令这样：
`mv /home/huo/Download/node-v4.4.4-linux-x64 /usr/local/node`
为了能够全局执行node和npm命令，需要建立软链接到可执行目录下，执行命令：
`echo $PATH`
可以看到：`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin`是linux全局执行目录，所以就选择在`/usr/local/bin`下建立软链接吧。

```ln -s /usr/local/node/bin/node /usr/local/bin/node
ln -s /usr/local/node/bin/npm /usr/local/bin/npm
```

这样就可以在任意目录下执行node和npm命令了，不过这样还没结束，使用npm全局安装包后，在命令行里执行会找不到命令，因为npm全局安装目录在node安装目录，也就是安装在了`/usr/local/node/lib/node_modules`目录下，所以还需要将这个目录添加的$PATH变量中，`sudo vim /etc/profile`在文件最下边添加`export PATH="/usr/local/node/bin:$PATH"`,此处的路径`/usr/local/node/bin`并不是上面说的`/usr/local/node/lib/node_modules`目录，因为npm全局安装以后会在node安装目录下的bin文件夹中建立软链接，直接在`$PATH`中添加npm的全局安装目录是无法找到可执行文件的。
