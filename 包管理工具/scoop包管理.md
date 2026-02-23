# 可以使用scoop安装的常用软件

注册右键菜单项目install-context.reg 和关联文件注册install-file-associations.reg 都在对应的app下 当然也有对应的删除注册项目
git注意事项 tortoisegit git路径 使用C:\Users\hello\scoop\shims C:\Users\hello\scoop\apps\git\current\bin\ 不要选择scoop/shims/git.exe

## scoop-directory scoop 搜索引擎 搜索软件以及相关对应的仓库

https://rasa.github.io/scoop-directory/search?q=Tortoisegit

```
//添加仓库
scoop bucket add extras
scoop bucket add versions
scoop bucket add java

// scoop bucket rm java 删除仓库

scoop bucket add jr https://github.com/joaoricarte/jr-bucket    // tortoisegit 对应的仓库
// scoop bucket add sdoog https://github.com/xrgzs/sdoog.git

//必须
scoop install main/7zip    //需要执行reg

scoop install aria2
# 启用 aria2 多线程下载
scoop config aria2-enabled true
# 设置分块数（核心多线程参数，建议 8-16）
scoop config aria2-split 16
# 每个服务器的最大连接数（与 split 配合，建议相同数值）
scoop config aria2-max-connection-per-server 16
# 最小分块大小（低于此大小不分块，1M 适配多数场景）
scoop config aria2-min-split-size 1M
# 下载重试等待时间（可选，默认 2 秒）
scoop config aria2-retry-wait 2
# 超时时间（可选，默认 60 秒）
scoop config aria2-timeout 60

scoop install git   //cmd下执行git config --global credential.helper manager  //需要执行reg
scoop install tortoisegit   // 缺少语言包 还是需要下载

//可用可无
scoop install ffmpeg
scoop install extras/vscode     //需要执行reg
scoop install extras/mobaxterm
scoop install extras/potplayer
scoop install extras/googlechrome
scoop install extras/anydesk

//删除缓存包  卸载后又想再安装的情况，不需要重复下载
scoop cache show  // 显示安装包缓存
scoop cache rm *  // 删除所有的安装包缓存
//删除旧版本软件，scoop更新软件不会将旧版移除，只是将创建一个链接指向新版本
scoop reset <java>[@<version>]
//更新软件
scoop update  *
//清理所有旧版本软件  有时候7zip会被其他软件使用导致无法删除可以重启
scoop cleanup *

```

## nodejs相关

```
scoop install versions/nodejs22

scoop install nvm  // 使用版本管理工具 下载
nvm install 24
```

## golang相关

```
scoop install go
```

## C/CPP 相关

- Threading library 线程库
  1. POSIX 基于 Windows 线程封装 pthread 接口 pthread 全版本支持
  2. WIN32（本机 Windows 线程，但缺少 POSIX 线程 / pthread.h） 全版本支持
  3. MCF 微软 MCF（Managed Concurrency Framework）封装，轻量级（自 GCC 13 起，另请参阅：MCF Gthread 库）

- runtime library C运行库
  1. MSVCRT 是旧的 对 C99/C11 标准支持差 但是兼容性好
  2. UCRT 适配 Win10+ 新特性，性能更优，bug 更少 推荐安装

- C++ 运行库
  1. libc++ clang/llvm 编译器使用的C++标准库
  2. libstdc++ 是gcc编译器使用的C++标准库

```
scoop install versions/mingw-winlibs-llvm    //默认是 MSVCRT
scoop install versions/mingw-winlibs-llvm-ucrt   //-----选择这个,推荐
```

## rust相关

- 运行库
  1. rust-gnu 默认支持 msvcrt ucrt需要配置 默认链接 libstdc++ 不直接支持 libc++ 也不建议
  2. rust-msvc 默认支持ucrt(1.59+) msvcrt需要配置，不直接支持 libc++/libstdc++
- rustup
  1. rustup 用于管理不同平台下的 Rust 构建版本并使其互相兼容,支持其他用于交叉编译的编译版本。 推荐安装这个,官方工具
  2. 手动添加 C:\Users\hello\scoop\persist\rustup\.cargo\bin 到环境变量-用户变量
  3. 依赖 mingw-winlibs-llvm-ucrt 安装gnu工具链

  ```
  scoop install versions/mingw-winlibs-llvm-ucrt //安装依赖gnu
  rustup default stable-x86_64-pc-windows-gnu
  rustup show  //查看默认工具链
  ```

  4. 测试

  ```

  rustc --version
  cargo --version
  cargo new rust-test
  cd rust-test
  cargo run

  ```

- 安装

  ```
  scoop install rustup   //默认是 rustup-msvc
  scoop install rustup-gnu  //推荐安装
  scoop install rustup-msvc
  ```

## zig相关

```
scoop install zig
```

## php相关

```
scoop install versions/php56
scoop install versions/php81
scoop install versions/php82  //提示 scoop install extras/vcredist2022
scoop install versions/php83
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
//推荐使用uv 管理工具安装 类似cargo  原来的包依赖管理工具  无法很好的管理以及兼容性 无法很好的解决依赖一个包多个版本
//source .venv/bin/activate  激活虚拟环境  win下是 .venv/Scripts/activate
// 或者直接 uv venv --seed 就进入.venv/Scripts/activate
//进入虚拟环境 才能执行python相关各种cli脚本命令  可以缺少pip 就用uv pip install pip
scoop install uv
uv python list //可以查看python版本  freethreaded是无GIL版本的
uv python install  //默认或安装最新版本  3.13 开始有一个自由线程也就是无GIL

scoop install versions/python27

scoop install versions/python312
```

## dotnet相关

```
scoop install versions/dotnet6-sdk

scoop install main/dotnet-sdk //最新 7，8哪里去了
```

# 另外需要手动的 暂时不可用

TortoiseGit  
Visual Studio  
Docker Desktop

```
//暂时不可用 只能安装官方下载
scoop install docker  //The 'dockerd' binary here only supports running Windows containers.
scoop install main/docker-compose
```
