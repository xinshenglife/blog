https://cloud.tencent.com/developer/article/1929195
# Node.js 的错误分为四类：

- 标准 JavaScript 错误，如 EvalError，SynctaxError，RangeError，ReferenceError，TypeError，URIError 
- 系统错误，如通过程序我们想打开一个文件，但是系统中不存在这个文件，就会抛出系统错误
- 通过程序代码 throw() 抛出的错误
- 断言错误，通过模块 assert 抛出的错误

# 同步代码使用 try catch 捕获异常
# 异步代码异常 注册Event error事件 或者最终process.on('uncaughtException')
# Promise异常捕获
- 使用Promise的catch方法内部消化；
- 使用async和await将异步错误转同步错误再由try catch捕获

- catch方法可以捕获 前面任意then方法抛出的错误
- then方法的第二个参数只能捕获上一层then方法抛出的错误，而不能捕获当前then方法里面抛出的错误
- then的第二个参数本来就是用来处理上一层状态为失败的
https://juejin.cn/post/7113537808207347719

## 大部分异步API一般都有一个回调函数 callback 作为其参数，而大部分同步API则不会
```
const fs = require('fs');
fs.readFile('/etc/passwd', (err, data) => {
  if (err) console.log(err);
  else console.log(data);
});

// 同步 API
fs.readFileSync('/etc/passwd');
```
大部分的异步方法都接受一个回调函数作为参数，我们通过该回调函数的第一个参数来判断是否发生了错误，如果是 null，则没有发生错误，如果不是 null，则调用该方法出现了错误，我们管这种回调叫做 Node.js 风格的回调
```
const fs = require('fs');

fs.readFile('/some/file/that/does-not-exist', function(err, data){
  if (err) {
    console.error('There was an error', err);
    return;
  }
  console.log(data);
});
```
如果想在异步方法的回调函数里面抛出错误，不要放在 try / catch 代码块中，这样不仅不会捕获到异常，而且未捕获的异常可能会造成程序停止
```
// 这样不会捕获异常:
const fs = require('fs');

try {
  fs.readFile('/some/file/that/does-not-exist', (err, data) => {
    // mistaken assumption: throwing here...
    if (err) {
      throw err; // 抛出错误，但是无法被捕获到
    }
  });
} catch (err) {
  // 无法被捕获到
  console.error(err);
}
```
因回调函数还没有执行，try / catch 代码已经执行完毕并退出，所以无法捕获错误。如果想捕获错误，可以使用 process.on('uncaughtException') （或者 Domain 模块来处理，但 Domain 模块已被新版本弃用，这里只是提一嘴，不推荐使用）方法来处理，可以把上面的代码改造成：
```
const fs = require('fs');

fs.readFile('/some/file/that/does-not-exist', (err, data) => {
    // mistaken assumption: throwing here...
    if(err) {
        throw err; 
    }
});

process.on('uncaughtException', (err) => {
    console.log(err);
})
```

异步 API 分为两种处理方式：一种是 Node.js 回调风格的 API，前面已有介绍；另一种方式：如果一个对象是一个 EventEmitter 时，如 Stream，Event 等模块，调用这个对象的异步方法时可以通过这个对象的 error 事件处理：
```
const net = require('net');
const connection = net.connect('localhost'); // EventEmitter

// 绑定 error 事件
connection.on('error', (err) => {
  // 处理 err
  console.error(err);
});

connection.pipe(process.stdout);
```
# koa2中全局异常
```
const koa = require("koa")
const Router = require("router")
const app = new koa();
const router = new Router();
class HttpException extends Error{
    constructor(msg = '服务器异常', errorCode = 1000,code = 400){
        super();
        this.msg = msg;
        this.code = code;
        this.errorCode = errorCode;
    }
}
//注册全局中间件
app.use(async(ctx, next) => {
    try{
        await next();
    }catch(error){
        if(error instanceof HttpExcetion){
            return ctx.body = error.msg
        }
        if(error.errorCode){
            console.log("捕获到异常")
            return ctx.body = errror.msg;
        }
    }
})
router.get('/login', (ctx, next) => {
    const path = ctx.request.query;
    
    // 主动抛出错误
    if(true){
        /*
        const error = new Error();
        error.errorCode = 1000;
        error.msg = "错误";
        throw error
        */
        throw new HttpException('登录错误',10000,500)
    }
})
app.use(router.routers())
```