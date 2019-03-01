### 一、安装gRPC
> $ go get -u google.golang.org/grpc

```
package google.golang.org/grpc: unrecognized import path "google.golang.org/grpc" (https fetch: Get https://google.golang.org/grpc?go-get=1: dial tcp 216.239.37.1:443: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.)
```
grpc的源码库迁移到了github上，所以需要手动下载了。[grpc-go](https://github.com/grpc/grpc-go)
直接git clone也不行的话就download吧！
```
$ git clone https://github.com/grpc/grpc-go.git grpc

Cloning into 'grpc'...
fatal: unable to access 'https://github.com/grpc/grpc-go.git/': OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
```
虽然grpc源码迁到了github上，但是包名并未随着修改，使用的还是**google.golang.org**，所以需要在**src**目录下新建google.golang.org目录，并把下载的**grpc-go-master.zip**解压到google.golang.org目录下，重命名为**grpc**

grpc包依赖[genproto](https://github.com/google/go-genproto)，下载解压后放在grpc同级目录，报名重命名为**genproto**

### 二、安装protoc编译器

从[protobuf](https://github.com/google/protobuf/releases)下载对应平台的编译器： protoc-<version>-<platform>.zip
比如我下载文件名为：**protoc-3.7.0-rc-2-win64.zip**，解压后的目录为：
```
E:.
│  readme.txt
│
├─bin
│      protoc.exe
│
└─include
    └─google
        └─protobuf
            │  any.proto
            │  api.proto
            │  descriptor.proto
            │  duration.proto
            │  empty.proto
            │  field_mask.proto
            │  source_context.proto
            │  struct.proto
            │  timestamp.proto
            │  type.proto
            │  wrappers.proto
            │
            └─compiler
                    plugin.proto
```
复制protoc.exe文件到GOPATH的bin目录下。这时就可以执行`protoc`命令了
```
$ protoc.exe -h
Usage: E:\go-work\bin\protoc.exe [OPTION] PROTO_FILES
Parse PROTO_FILES and generate output based on the options given:
  -IPATH, --proto_path=PATH   Specify the directory in which to search for
                              imports.  May be specified multiple times;
```
但是还需要安装**protoc-gen-go**插件，可以产生go相关代码， 除上述序列化和反序列化代码之外， 还增加了一些通信公共库。
>$ go get -u github.com/golang/protobuf/protoc-gen-go

还是不能正常下载的话，那就老办法，手动下载[protoc-gen-go](https://github.com/golang/protobuf)

解压后将**protoc-gen-go**目录复制到src/github.com/目录下。
>$ cd protoc-gen-go
>$ go install 

会在$GOPATH/bin目录下生成protoc-gen-go文件。

到此，go语言开发grpc所需的工具已经安装完成。

```js
$(function () {
      //bootstrap WYSIHTML5 - text editor
      $('.textarea').wysihtml5();
      $(".J-submit").on("click", function () {
        var title = $("#art_title").val();
        var content = $("#art_content").val();
        $.post("/admin/article", { title: title, content: content }, function (result) {
          if (result.code == 0) {
            // layer.msg("新建成功！", { icon: 6 });
            location.href="/admin/articles-route"
          } else {
            layer.msg("新建失败！" + result.msg, { icon: 5 });
          }
        }, "json");
      })
    })
```