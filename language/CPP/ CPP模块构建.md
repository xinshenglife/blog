```
module;    
#include ""; //绝对路径  
export module A;  //模块名A 相对路径  
```
 
//Qtbase 最小的   Qtcore 最核心的 相当于基础类库 corelib  
或者chrome开源项目 abseil基础类库

module 应该就是和golang 一样 模块也就是 npm install moduleName

C++  namepsace 建立和文件的关系

import moduleName;
using namespace namespaceName;
namespace second_space{
   #include ""; //绝对路径   建立文件夹关系
}


using namespace second_space;
second_space::adfasdf()
项目内组织

类似和golang  import 模块 自动解析namespaceName==packageName

全局的namepsace 本地项目  必须定义moduleName
rtmp

using rtmp/name1  就是root目录/name1 文件夹下 或者 某个文件夹下有namepsace name1   把项目下root所有文件读取 组成namespace list 不重复


moduleName===对应包名     vendor 作为第三方依赖文件夹 放入依赖的每一个moduleName  一个moduleName 下搞个工具自动扫描所有namespace 构建moduleName export

```
module;    
#include "namespace1"; //绝对路径  
#include "namespace2"; //绝对路径  
#include "namespace3"; //绝对路径  
export namespace1;
export namespace2;
export namespace3;
export module A;  //模块名A 相对路径  
```

项目文件夹下 只要using namespace1  引入第三方则需要import
namespace s
using namespace chrome=std 命名空间重新命名  按照约定使用 json 或者yaml 等格式文件处理依赖  GitHub仓库 类似  xrepo 一样

cpp项目 所有都会合并一个module  这样namespace 就不用手动#include了 直接在项目using就好了 模块名 golang类似 github.com/aa/cc


MinGW本身是个第三⽅的Windows SDK（就是windows.h，libgdi32.a这些Windows API头⽂件
和库）。出现的原因就是微软 ⾃⼰的Windows SDK授权跟GPL不兼容，不允许重分发，⾥⾯的头
⽂件还可能存在MSVC独有扩展，GCC没法⽤。
另外，MinGW也充当了libc (C标准库 )的⻆⾊。（MinGW⼴义上说是链接到系统的
msvcrt.dll/Universal CRT，但为了适配GCC在上⾯hook了很多魔改的补丁。⽐如MinGW的
libmsvcrt ，printf是改过的，⽀持C99格式字符串 ）
注意，MinGW本身并不是编译器 ，它只是⼀套头⽂件和库。⾥⾯也没有C++标准库，libstdc++是
GCC的东⻄。
通常我们说的MinGW⼯具链 ，是由第三⽅将MinGW和适配的编译器、链接器 组合在⼀起的整合
包。早期MinGW是专为GCC做的，所以⼀般提到MinGW⼯具链，就是MinGW(-
w64)/GCC/binutils/libstdc++。
但如今Clang/LLVM成熟之后，问题就复杂很多了。
Clang可以仅替换掉GCC，结果是MinGW-w64/Clang/binutils/libstdc++，如MSYS2 UCRT64⾥的
clang。
Clang/LLVM也可以把整套⼯具链换掉，连C++标准库也换成⾃⼰的libc++，结果是⼀个没有G
（GNU）的MinGW⼯具链，MinGW-w64/Clang/LLVM/libc++，如MSYS2 CLANG64⾥的clang，
或者llvm-mingw⼯具链。
特别的，MinGW-w64/Clang/LLVM/libc++是⽬前唯⼀能target Windows on ARM的MinGW⼯具
链。
Clang在以MinGW模式⼯作时，triplet 是*-pc-windows-gnu，冒充GCC，所以会定义__GNUC__。
Clang也有⼀个MSVC模式，triplet是*-pc-windows-msvc，冒充MSVC，使⽤的是微软的C++ ABI
以及官⽅的Windows SDK，此时就会定义__MSC_VER。但我不清楚这时候⽤的是VC++的运⾏时
（msvcp*.dll）还是libc++。
Clang会伪装别的编译器，但实际做编译的还是它⾃⼰，它⾃⼰有__clang__的宏标识