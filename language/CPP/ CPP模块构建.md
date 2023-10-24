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