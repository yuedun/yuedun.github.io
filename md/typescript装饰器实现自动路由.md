# typescript装饰器实现自动路由

### 基本思路

路由书写格式：
```
// home-roter.ts
export default class Routes {
    @route({
        path: "/",
        method: "get"
    })
    static index(req: Request, res: Response) {

    }

    @route({
        path: "/home",
        method: "get"
    })
    static home(req: Request, res: Response) {

    }
```
1. 利用文件系统读取某一目录下的路由文件，循环使用require导入文件（不能使用import导入，因为可能会导入不成功，
应该是ts没有使用到的文件不会导入）。

2. 获取文件中的class类，循环类的每一个方法method，并添加到`router = express.Router();`最后app.use(router)















