> RockMongo是一个PHP5写的MongoDB管理工具。

鉴于百度bce的mongodb数据使用了RockMongo来管理数据，就以此来说明。

#### 查询操作
点击某个collection后的默认画面：
![img-rockmongo1](../images/rockmongo1.png)

查询界面很简单，关键是怎么写查询语句？
![img-rockmongo2](../images/rockmongo2.png)
点击文本可以查看所有数据字段，查询title为“测试”文档：
```
array(
	'title' => '测试'
)
```
字段名要加引号，中间使用`=>`而不是使用`:`分割，需要查询的值也需要注意，字符串加引号，数字不加，如果类型不匹配就会查不出数据。查询结果有一条数据
![img-rockmongo3](../images/rockmongo3.png)

#### 修改操作
动作中选择“modify”
![img-rockmongo4](../images/rockmongo4.png)
再查询一下
![img-rockmongo5](../images/rockmongo5.png)

基本操作就是这样，删除操作不用说也应该能知道怎么做了。




























