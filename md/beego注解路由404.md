beego注解路由匹配不到，返回404页面
router.go使用了两种方式注册路由：
```go
ns := beego.NewNamespace("/admin",
	beego.NSRouter("/", &controllers.UserController{}, "get:Welcome"),
	beego.NSInclude(
		&controllers.UserController{},
	),

```
controller中的路由注解设置：
```go
// @router /admin/user/get-all-user [get]
func (c *UserGroupController) GetAllUser() {
	user := new(User)
	users, err := user.GetUserList()
	if nil != err {
		c.Data["json"] = ErrorMsg(err)
	}
	c.Data["json"] = users
	c.ServeJSON()
}
```
使用上面的方式注册路由后结果是nomatch

最终结果显示上面的注解路由时错误的，下面是正确的注册方式：
问题在于controller的注解写法，如果该路由在namespace下，则不能在注解中拼接命名空间前缀，框架会自动拼接。
即`/admin`为命名空间，注解中只需写`/user/get-all-user`，不能这样写`/admin/user/get-all-user`
```go
// @router /user/get-all-user [get]
func (c *UserGroupController) GetAllUser() {
	user := new(User)
	users, err := user.GetUserList()
	if nil != err {
		c.Data["json"] = ErrorMsg(err)
	}
	c.Data["json"] = users
	c.ServeJSON()
}
```
