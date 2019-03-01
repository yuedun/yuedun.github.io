# nodejs中promise的if流程控制

Promise虽然解决了原生回调的金字塔写法，但是并不能很好的处理`if else`流程，相比起Java等同步方式编写还是有点难度，不过还是有方法写出优雅的代码。
下面用了一个不复杂的例子来说明：
> 假设一个人去银行开户，存钱，如果这个人是普通用户，需要先1.排队等候，2.开户，3.存钱。
如果是VIP用户，直接进行第2，3步操作。
如果普通用户是黑名单用户不给开户办卡。

```javascript
var fs = require("fs");

function queue(){
	return new Promise((resolve, reject)=>{
		fs.readFile("../queue.txt", "utf8", (err, data)=>{
			if (err) reject(err);
			console.log(data.toString());
			resolve(data.toString());
		})
	})
}
function account(id){
	return new Promise((resolve, reject)=>{
		fs.writeFile('../account.txt', id+'一个新账户', (err) => {
			if (err) reject(err);
			console.log('办卡成功!');
			resolve();
		});
	})
}
function money(id){
	return new Promise((resolve, reject)=>{
		fs.writeFile('../money.txt', id+'存了10万', (err) => {
			if (err) reject(err);
			console.log('存款成功!');
			resolve();
		});
	})
}

//入口
function enter(id) {
	Promise.resolve()
	.then(()=>{
		if (id=="common") {
			return queue()
		} else if (id =="vip") {
			console.log("我是VIP用户，不需要排队等待验证身份直接开户存钱")
		}
	}).then(user=>{
		if (user =="黑名单用户") {
			throw "禁止开户"
		}
		return user;
	}).then(user=>{
		return account(id);
	}).then(()=>{
		return money(id);
	}).catch(err=>{
		console.log("回家")
	})
}

// enter("vip");
enter("common");
```
不通的用户会有不同的判断，走不同的流程，但是各类用户又有相同的操作，所以有if条件判断的逻辑单独放到一个then中处理，返回处理结果，如果后续then不需要处理结果也可以不返回，当做没有这个then。
