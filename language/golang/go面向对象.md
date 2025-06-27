
# 包依赖管理工具 go mod 以及下载加速镜像 go env -w GO111MODULE=on  go env -w GOPROXY=https://goproxy.cn,direct

# import规则   
- 点操作 点操作import( . “fmt” )  省略fmt.  之前是fmt.Println(“hello world”)   可以Println(“hello world”) 
- 下划线 _ 只执行包init函数 不引入包其他  import (  _ "foo/bar/baz" ) 
- 别名 import( f “fmt” ) f.Println(“hello world”) 
- import 导入后面的是目录，非 package名，使用的时候却是使用package名引用  所以规范一般都建议目录名和package名 保持一致
``` 
import "github.com/BurntSushi/Toml"   
//Toml是目录名字 一般规范建议 Toml改成和package名一致 toml   
//最后一个Toml是对应的实际目录名 前面没有什么特殊关系
toml.ParseError()   //toml是package名
```
# package规则
- 同一目录下，所有源文件必须使用相同的包名，一个文件夹下只能有一个 package
- 如果多个文件夹下有同名 package，其实只是彼此无关的 package，同时引入同名package 可以使用别名
- 大写字母开头的变量方法暴露到包外，包内大小写随意
# OOP
    OOP目的原先是用继承和多态，实现复用以及不同，class和多继承已经满足了
    - class是为构造模板封装  
    - 继承是为了代码复用   
    - 多态是为了不同对象使用同样的行为方法，进行区别就是一种接口规范

---
+  struct结构体就是和class 差不多   组合和多继承差不多  但是减少冗余    各个struct只关注自己的就好，使用的时候直接调用过来  golang 需要注意的是 方法是 在方法名前加上接收者也就structName
    1. golang中new、make及取地址符&  make是返回引用类型的指针
    new 和& 区别在初始化 new是初始化0值，不能指定   & 可以初始化不为0可以手动指定
    2. 初始化  变量初始化->init()->main()
    https://zhuanlan.zhihu.com/p/34211611
    3. 方法名首字母要大写才是public 否则都是私有的
    4. 异常处理 recover()  接受异常处理   panic() 相当于throw抛异常

+ 多继承继承的过于浪费过于冗余  单继承的不能完全表达现实多样性 限制了多样性，golang中所谓的组合本质就是多继承
使用组合 主要就是在struct a中直接引入 另外一个结构体struct b  然后关联方法  方法对应struct  一样实现a实例对象调用b方法  减少了冗余
+ 剩下一个问题就是多态 多态是指代码可以根据类型的具体实现采取不同行为的能力。
使用的是interface 来实现的   interface就像泛型一样 任何类型都可以进入 interfaceA.typeB.methodC()  methodC要在interfaceA中定义  更像是一种约定 和  abstract 差不多 必须实现接口里的所有方法
```
type Base struct {
    // nothing
}
 
func (b *Base) ShowA() {    //方法
    fmt.Println("showA")
    b.ShowB()
}
func (b *Base) ShowB() {
    fmt.Println("showB")
}
 
type Derived struct {
    Base   //组合 也就是继承
}
 
func (d *Derived) ShowB() {
    fmt.Println("Derived showB")
}
 
func main() {  //函数
    d := Derived{}
    d.ShowA()
}
```

总结：还是原来那一套 总不过换了说法  struct就是class  组合就是多继承  interface就是abstract ------php traits和class区别 traits 不能实例化废了 不如C++