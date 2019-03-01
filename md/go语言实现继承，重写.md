# go语言实现继承，重写

以实际遇到过得情况为例，用户的数据结构有createdAt为日期类型，经过反复的格式化处理，在页面上输出的还是`2017-05-31 06:49:09 +0800 CST`这种格式，所以就想到了重写。先是利用go的继承将User的数据结构继承都UserPojo里，再单独对createdAt进行修改，重写为string类型。
```go
package main

import "fmt"
import "time"

type User struct {
    name string
    age int
    createdAt time.Time
}

type UserPojo struct {
    User
    createdAt string
}

func (user *User) getName(){
    fmt.Println("获取用户名：", user.name)
}

func main()  {
    user := new(User)
    user.name="张三"
    user.age=26
    user.createdAt=time.Now()
    fmt.Println("user.createdAt",user.createdAt)

    userpj := new(UserPojo)
    userpj.User = *user
    userpj.createdAt = user.createdAt.Format("2006-01-02 15:04:05")
    fmt.Println("userpj.createdAt",userpj.createdAt)
}

//输出
//user.createdAt 2017-06-17 10:39:29.5294 +0800 CST
//userpj.createdAt 2017-06-17 10:39:29

```