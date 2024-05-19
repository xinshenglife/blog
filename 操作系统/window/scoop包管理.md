# 可以使用scoop安装的常用软件
注册右键菜单项目install-context.reg 和关联文件注册install-file-associations.reg 都在对应的app下 当然也有对应的删除注册项目

git注意事项  tortoisegit  git路径 使用app/git/current/bin/git.exe  不要选择scoop/shims/git.exe
```
//添加仓库
scoop bucket add extras
scoop bucket add versions
scoop bucket add java
//必须
scoop install main/7zip    //需要执行reg
scoop install git   //cmd下执行git config --global credential.helper manager  //需要执行reg
scoop install ffmpeg 


scoop install extras/mobaxterm
scoop install extras/potplayer  
//可用可无
scoop install extras/googlechrome
scoop install extras/vscode     //需要执行reg
scoop install extras/anydesk
```
## C/CPP 相关
### C/CPP库 
- MSVCRT （Microsoft Visual C++ Runtime）
- UCRT Universal C Runtime  通用 C 运行时---一般默认是这个除非兼容性才选MSVCRT

- WinLibs  WinLibs standalone build of GCC and MinGW-w64 for Windows 独立构建
### 线程库
- POSIX（与其他平台的最佳兼容性）
- WIN32（本机 Windows 线程，但缺少 POSIX 线程 / pthread.h）
- MCF（自 GCC 13 起，另请参阅：MCF Gthread 库）
- MSVCRT（Microsoft Visual C++ Runtime）默认在所有 Microsoft Windows 版本上可用，但由于向后兼容性问题一直停留在过去，不兼容 C99 并且缺少一些功能。它不兼容 C99，例如 printf() 函数系列，但是......mingw-w64 提供了替换功能，使东西在很多情况下兼容 C99
它不支持 UTF-8 区域设置
与 MSVCRT 链接的二进制文件不应与 UCRT 链接的二进制文件混合，因为内部结构和数据类型不同。相同的规则适用于 MSVC 编译的二进制文件，因为 MSVC 默认情况下使用 UCRT（如果未更改）。
在每个 Microsoft Windows 版本上开箱即用。
UCRT（通用 C 运行时）是一个较新的版本，Microsoft Visual Studio 也默认使用它。它的工作和行为应该就像代码是用 MSVC 编译的一样。
在构建时和运行时与 MSVC 具有更好的兼容性。
默认情况下，它仅在 Windows 10 上提供，对于旧版本，您必须自己提供它或取决于安装它的用户。

MSVCRT与UCRT --这是微软Windows上C标准库的两个变体
libc++ clang编译器使用的C++标准库  libstdc++是gcc编译器使用的C++标准库

MINGW  使用msvcrt C标准库 和gcc一致的C++标准库 libstdc++
clang gcc都是使用UCRT（通用）C标准库　具体参考图
```
scoop install versions/mingw-winlibs-llvm    //默认是 MSVCRT
scoop install versions/mingw-winlibs-llvm-ucrt          //-----选择这个 
scoop install versions/mingw-winlibs-llvm-ucrt-mcf //里面带有 LLVM/Clang/LLD/LLDB 套件 UCRT mcf----最新的已经移除掉这个选项-------------XXXXXXXXXXXXXXXXX
```
## Java android-studio 相关
```
scoop install extras/android-studio  //安装这个会出现下面提示 android-clt
scoop install android-clt   //安装这个会出现下面提示 openjdk17
scoop install java/openjdk17
scoop install java/openjdk21
//上述安装好了 打开Android-studio sdk管理器 手动指定到android-clt位置    show package detail可以看到指定 cmd tools 版本的   flutter doctor 都可以通过 unity3d也通过

//自定义Java不同包  不能像mac那样 
scoop bucket add java
scoop install java/corretto-jdk
```
## dart 
```
scoop install main/dart //不用安装flutter自带
scoop install extras/flutter
```
## python相关
```
scoop install versions/python27

scoop install versions/python35
scoop install versions/python36
scoop install versions/python37
scoop install versions/python38
scoop install versions/python39
scoop install versions/python310  
scoop install versions/python311
scoop install versions/python312
```
## php相关
```
scoop install versions/php54
scoop install versions/php55
scoop install versions/php56

scoop install versions/php70
scoop install versions/php71
scoop install versions/php72
scoop install versions/php73
scoop install versions/php74

scoop install versions/php81
scoop install versions/php82  //提示 scoop install extras/vcredist2022
scoop install versions/php83
```
## nodejs相关
```
scoop install versions/nodejs4
scoop install versions/nodejs5
scoop install versions/nodejs6
scoop install versions/nodejs7
scoop install versions/nodejs8
scoop install versions/nodejs9
scoop install versions/nodejs10
scoop install versions/nodejs11
scoop install versions/nodejs12
scoop install versions/nodejs13
scoop install versions/nodejs14
scoop install versions/nodejs15
scoop install versions/nodejs16
scoop install versions/nodejs17
scoop install versions/nodejs18
scoop install versions/nodejs19
scoop install versions/nodejs20
```
## golang相关
```
scoop install main/go 
```
## dotnet相关
```
scoop install versions/dotnet2-sdk
scoop install versions/dotnet3-sdk
scoop install versions/dotnet4-sdk
scoop install versions/dotnet5-sdk
scoop install versions/dotnet6-sdk

scoop install main/dotnet-sdk //最新 7，8哪里去了
```

## rust相关
```
scoop install main/rust  //默认是msvc 工具链
scoop install main/rust-msvc
scoop install main/rust-gnu  //使用这个gnu工具链 就可以服用 scoop install versions/mingw-winlibs-llvm-ucrt-mcf
```
#  另外需要手动的 暂时不可用
TortoiseGit  
Visual Studio  
Docker Desktop
```
//暂时不可用 只能安装官方下载
scoop install docker  //The 'dockerd' binary here only supports running Windows containers.
scoop install main/docker-compose
```