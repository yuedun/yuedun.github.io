typescript（以下简称ts）中泛型如何使用？（以下代码为ts书写）
个人认为开发语言中一些高级特性如果在不太理解的情况下不使用也无妨，无非是代码写的多一点，烂一点。但是我想作为程序猿大家还是会有所追求的。就以泛型来说，不使用也能正常开发，只不过在个别情况下需要写几份看起来相同的代码。比如在不使用泛型的情况下要求函数参数为number类型，并且返回number。
```
function a(args: number ): number {
	return args;
}
console.log(a(123));
```
又有另外一个要求，参数为string类型，并且返回string
```
function b(args: string ): string {
	return args;
}
console.log(b("sdg"));
```
如果还有其他类似的要求，就要不停的写格式类似的代码，name改进一下：
```
function c(args: any ): any {
	return args;
}
console.log(c("sdg"));
```
这样是可以接受任何类型参数并且返回，但缺点是可以知道能传入任何类型而不知道返回的具体类型是什么，只知道是any类型。将上面的`c`函数稍作修改：
```
function c(args: any ): any {
    let n = args + "变成了字符串";
    //甚至更多的处理
	return n;
}
console.log(c(1));
```
`c`函数参数是数字，返回的可能是其他类型。
知道前面几种写法的不足再对比一下泛型的写法：
```
function d<A>(args: A ): A {
    let n = args + "变成了字符串";
    //甚至更多的处理
	return n;
}
console.log(d(1));
```
![编辑器中的提示](C:\\Users\\Administrator\\Pictures)
`d`函数要求参数是A类型，并且返回A类型，但是实际返回的类型可能由number类型变成了string类型，所以提示错误。其中`<>`中可以是任意大小写的英文，中文也行，但数字不行，比如：
```
function d<Az>(args: Az ): Az {
    let n = args + "变成了字符串";
    //甚至更多的处理
	return n;
}
console.log(d(1));
```
`<>`中的值是指代某种基础类型或任何自定义类型，它只是一种形式。也可以有下面的形式：
```
function d<T, U>(args: T,ar: U): U {
	return ar;
}
console.log(d(2, "aaaa"))
```
其实就是提供一种形式供后面代码使用，没有提供就不能使用。