# OOP  封装 继承/组合 多态
上述都是为了复用和组织代码所用,任何一种技术出现都是为了解决相应的问题
1. 封装是为了 被人复用 所带来的隔离 则更好的组织,每个人分工更加明确. 使用函数基本上就解决了.但是还有更高层次的封装  class  对象 等等
2. 继承和组合 为了复用别人的代码 继承一大堆发现带来的复杂问题 推荐使用组合,组合就相当于去掉这些复杂性
3. 多态 是为了封装 统一一个类型,带入参数不同 调用不同类型具体实现   zig使用完整的泛型解决了 go 实现的不是很完善  则使用interface来实现多态  其他则oop实现

# gc和并发
4. defer 就是函数的gc一样 最后执行一定会释放资源 gc或者文件句柄
5. 多进程 多线程  go协程  async+await 状态机  事件循环  io_uring 跨平台

# zig并发  兼容所有的 并且避开async的传染 
1. std.io —— 统一 IO 接口
2. std.io.Mode —— 自动选择最佳异步引擎
    Linux: io_uring
    Windows: IOCP
    macOS/BSD: kqueue
3. std.Thread —— 多线程（不变）
4. io.async() io.wait() 还有个阻止async传染关键字
5. io.concurrency()  类似go协程

# zig 泛型是非常完善的