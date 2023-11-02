# 可以使用scoop安装的常用软件
注册右键菜单项目install-context.reg 和关联文件注册install-file-associations.reg 都在对应的app下 当然也有对应的删除注册项目

git注意事项  tortoisegit  git路径 使用app/git/current/bin/git.exe  不要选择scoop/shims/git.exe
```
scoop install main/7zip    //需要执行reg

scoop install extras/mobaxterm

scoop install git   //cmd下执行git config --global credential.helper manager  //需要执行reg

scoop install extras/vscode     //需要执行reg

scoop install ffmpeg 

scoop install extras/googlechrome
scoop install extras/potplayer   
scoop install extras/anydesk

scoop install go //开发软件 go  php nodejs python 等等

//还是安装软件Docker Desktop Installer.exe
scoop install docker  //The 'dockerd' binary here only supports running Windows containers.
scoop install main/docker-compose

scoop bucket add versions
scoop install versions/mingw-winlibs-llvm-ucrt-mcf //里面带有 LLVM/Clang/LLD/LLDB 套件 UCRT mcf
```
#  另外需要手动的

TortoiseGit  Visual Studio  Docker Desktop Installer.exe
## C/CPP库 
- MSVCRT （Microsoft Visual C++ Runtime）
- UCRT Universal C Runtime  通用 C 运行时---一般默认是这个除非兼容性才选MSVCRT

- WinLibs  WinLibs standalone build of GCC and MinGW-w64 for Windows 独立构建
## 线程库
- POSIX（与其他平台的最佳兼容性）
- WIN32（本机 Windows 线程，但缺少 POSIX 线程 / pthread.h）
- MCF（自 GCC 13 起，另请参阅：MCF Gthread 库）

MSVCRT（Microsoft Visual C++ Runtime）默认在所有 Microsoft Windows 版本上可用，但由于向后兼容性问题一直停留在过去，不兼容 C99 并且缺少一些功能。

它不兼容 C99，例如 printf() 函数系列，但是......
mingw-w64 提供了替换功能，使东西在很多情况下兼容 C99
它不支持 UTF-8 区域设置
与 MSVCRT 链接的二进制文件不应与 UCRT 链接的二进制文件混合，因为内部结构和数据类型不同。相同的规则适用于 MSVC 编译的二进制文件，因为 MSVC 默认情况下使用 UCRT（如果未更改）。
在每个 Microsoft Windows 版本上开箱即用。
UCRT（通用 C 运行时）是一个较新的版本，Microsoft Visual Studio 也默认使用它。它的工作和行为应该就像代码是用 MSVC 编译的一样。
在构建时和运行时与 MSVC 具有更好的兼容性。
默认情况下，它仅在 Windows 10 上提供，对于旧版本，您必须自己提供它或取决于安装它的用户。

scoop 提供 
scoop install versions/mingw-winlibs-llvm
scoop install versions/mingw-winlibs-llvm-ucrt


scoop install extras/flutter
scoop install android-clt
scoop install extras/android-studio
scoop bucket add java
scoop install java/openjdk
scoop install java/corretto-jdk