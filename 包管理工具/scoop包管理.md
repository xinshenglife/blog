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

scoop install extras/hibit-uninstaller
scoop install extras/hibit-system-information


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

# C/CPP 相关
## Scoop 中 `main/gcc`、`main/mingw`、`main/mingw-winlibs` 的区别

### 概述

这三个 Scoop 包都是 Windows 下的 GCC / MinGW 工具链，但它们的定位和完整度不同：

- `main/gcc`：极简编译器包，只提供编译、链接核心工具。
- `main/mingw`：标准基础工具链，适合作为可用的基础开发环境。
- `main/mingw-winlibs`：完整的 WinLibs 发行版，捆绑调试、构建、缓存等大量开发工具。

可以简单理解为：

> `main/gcc` ≈ 只有编译器核心  
> `main/mingw` ≈ 基础工具链  
> `main/mingw-winlibs` ≈ 开箱即用的完整开发环境

---

### 核心对比

| 特性 | `main/gcc` | `main/mingw` | `main/mingw-winlibs` |
| :--- | :--- | :--- | :--- |
| **本质** | 极简编译器包 | 标准基础 MinGW 工具链 | 完整 WinLibs 发行版 |
| **上游来源** | Nuwen MinGW Distro | MinGW-w64 标准构建 | WinLibs |
| **核心定位** | 只提供编译和链接能力 | 提供一套能工作的基础环境 | 提供开箱即用的完整开发套件 |
| **主要组件** | `gcc`、`g++`、`binutils`（`ld`、`ar` 等） | `gcc`、`g++`、`binutils`，通常包含 `make` | 在基础工具链上额外捆绑 `gdb`、`make`、`cmake`、`ninja`、`ccache`、汇编器等 |
| **是否包含 `make`** | 否 | 通常包含 | 包含 |
| **是否包含 `gdb`** | 否 | 通常不包含 | 包含 |
| **是否包含 CMake / Ninja** | 否 | 通常不包含 | 通常包含 |
| **是否包含 `ccache`** | 否 | 通常不包含 | 通常包含 |
| **体积** | 最小 | 中等 | 最大 |
| **适合场景** | 只需要编译器核心，构建流程自行管理 | 需要基础 `make` 构建环境 | 希望一步到位获得完整命令行开发环境 |

---
## Scoop 中 Clang/LLVM 相关包区别总结

### 涉及包

- `scoop install main/clangd`
- `scoop install main/llvm`
- `scoop install versions/llvm-full`
- `scoop install versions/mingw-winlibs-llvm-ucrt`

这四个包虽然都与 LLVM/Clang 有关，但定位截然不同：

- `clangd`：单一语言服务器，只做 IDE 智能提示。
- `llvm`：官方 LLVM 核心发行版，纯 LLVM/Clang 工具链。
- `llvm-full`：LLVM 完整构建版，额外包含开发库和非常规工具，面向基于 LLVM 库的二次开发。
- `mingw-winlibs-llvm-ucrt`：本质是 **GCC 为主、LLVM 为辅** 的集成工具链，LLVM 是捆绑的附带组件，且版本可能较旧。

---

### 快速定位

| 包名 | 本质 | 核心内容 | 与 LLVM 的关系 |
| :--- | :--- | :--- | :--- |
| **`main/clangd`** | 单一语言服务器 | 仅 `clangd` | 只提供 IDE 智能提示，**不含编译器** |
| **`main/llvm`** | 官方 LLVM 核心发行版 | `clang`、`clangd`、`clang-tidy` 等 | **纯 LLVM**，Windows 上的标准选择 |
| **`versions/llvm-full`** | LLVM 完整构建版 | 在核心工具之外，额外包含**开发库与不常用工具** | **纯 LLVM**，面向基于 LLVM 库的二次开发 |
| **`versions/mingw-winlibs-llvm-ucrt`** | **GCC + LLVM 集成工具链** | `gcc`、`g++`、`gdb`、**LLVM/Clang/LLD/LLDB** | **GCC 是主体，LLVM 是捆绑的附带组件** |

---

## 逐项解析

### 1. `main/clangd`：只做一件事

它只提供 `clangd` 这个可执行文件，用于为 VS Code 等编辑器提供代码补全、跳转和诊断。

- **不包含** `clang` 编译器本身。
- 不能用来编译程序。
- 适合已经通过其他途径（如 Visual Studio）获得编译器，只想启用 `clangd` 智能提示的用户。

### 2. `main/llvm`：纯粹的官方 LLVM 工具链

这是 Scoop 中获取 **纯正 LLVM/Clang 编译器**的标准入口。

安装后可以使用：

- `clang`
- `clang++`
- `clangd`
- `clang-tidy`
- 其他 LLVM 工具

需要注意：

- 它通常**不包含 Windows 平台完整的标准库和链接器**。
- 实际编译时仍需要依赖系统中已有的 MSVC 或 MinGW 来提供基础设施。
- 当前 `main/llvm` 版本为 **23.1.3**，与 LLVM 官方最新稳定版基本同步。

### 3. `versions/llvm-full`：为“使用 LLVM 库”而生的完整版

它的描述特别强调：

> full build with libraries and tools not normally used in a toolchain

这意味着它在常规编译器工具之外，额外包含了供开发者**在自己的程序中链接 LLVM 库**所需的材料，通常包括：

- LLVM 头文件
- 静态库/动态库
- CMake 配置文件
- 不常随普通工具链分发的工具

适合场景：

- 编写基于 LLVM 库的二次开发工具
- 自定义代码分析器
- 新的编译器前端
- 代码转换工具

注意：

- `llvm-full` 提供的是**编译好的二进制依赖库**，不是 LLVM 源代码。
- 它适合“你的项目依赖 LLVM 库，但你不修改 LLVM 本身”的场景。
- 如果你要从源码构建或修改 LLVM，需要的是 LLVM 源代码仓库和完整构建环境。

---

### 选择建议

| 需求 | 推荐包 |
| :--- | :--- |
| 只要编辑器的代码补全、跳转、诊断 | `main/clangd` |
| 要用 `clang` 编译程序，并希望版本较新 | `main/llvm` |
| 要做基于 LLVM 库的二次开发 | `versions/llvm-full` |
| 想要一个以 GCC 为主、同时顺带拥有 Clang 的完整开箱即用环境，且不介意 LLVM 版本稍旧 | `versions/mingw-winlibs-llvm-ucrt` |
| 需要 GCC 的完整生态，同时偶尔使用 Clang | `versions/mingw-winlibs-llvm-ucrt` |
| 需要最新 LLVM/Clang，同时也要 GCC | 分别安装 `main/llvm` 和 `main/mingw-winlibs` 或 `main/mingw` |

---

### 总结

- `main/clangd`：单一语言服务器，不含编译器。
- `main/llvm`：纯 LLVM/Clang 核心工具链，版本较新（23.1.3）。
- `versions/llvm-full`：LLVM 完整构建版，包含开发库，适合基于 LLVM 的二次开发。
- `versions/mingw-winlibs-llvm-ucrt`：GCC 为主、LLVM 为辅的集成工具链，开箱即用，但捆绑的 LLVM 版本可能较旧（15/16）。

### 总结选择 gcc相关就选择 scoop install mingw-winlibs  llvm+clang相关选择 scoop install llvm
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

  rustup toolchain list  //查看已安装的工具链
  rustup toolchain install stable-x86_64-pc-windows-gnu  //安装gnu工具链
  rustup default stable-x86_64-pc-windows-gnu  //后面是工具链

  rustup target list //查看支持的目标平台
  rustup target add x86_64-pc-windows-gnullvm //后面可以多个target
  // .cargo/config.toml 配置文件实现默认 target
  # 项目根目录的 rust-toolchain.toml
  [toolchain]
  channel = "nightly"  # 项目专用工具链
  targets = ["x86_64-pc-windows-gnullvm"]  # 项目常用 target

  rustup show //查看默认工具链和目标平台
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

//自定义Java不同包 不能像mac那样
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
# microsoft-coreutils
scoop install main/microsoft-coreutils
# 另外需要手动的 暂时不可用

TortoiseGit  
Visual Studio  
Docker Desktop

```
//暂时不可用 只能安装官方下载
scoop install docker  //The 'dockerd' binary here only supports running Windows containers.
scoop install main/docker-compose
```
