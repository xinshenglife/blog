# Windows 下 GCC / Clang / MSVC / MinGW 与运行库总结
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
## 1. 核心分层

| 层级 | 内容 |
|---|---|
| 编译器 | `gcc/g++`、`clang/clang++`、`cl.exe` |
| 工具链 / 目标平台 | MSVC、MinGW-w64、Cygwin/MSYS2 |
| C 运行时 | `msvcrt.dll`、UCRT(`ucrtbase.dll`)、VCRuntime |
| C++ 标准库 | MSVC STL、`libstdc++`、`libc++` |
| 运行库 / 可再发行包 | VC++ Redist、`libstdc++-6.dll`、`libgcc_s_*.dll`、`libwinpthread-1.dll` |

---

## 2. 对照表

| 工具链 | 编译器 | C++ ABI | C 运行时 | C++ 标准库 | 典型依赖 |
|---|---|---|---|---|---|
| MSVC | `cl.exe` | MSVC ABI | UCRT + VCRuntime | MSVC STL | `vcruntime140.dll`、`msvcp140.dll`、`ucrtbase.dll` |
| MinGW-w64 + GCC | `gcc/g++` | GNU/Itanium | `msvcrt.dll` 或 UCRT | `libstdc++` | `libstdc++-6.dll`、`libgcc_s_*.dll`、`libwinpthread-1.dll` |
| Clang + MSVC 模式 | `clang-cl` | MSVC ABI | UCRT + VCRuntime | MSVC STL | 同 MSVC |
| Clang + MinGW 模式 | `clang --target=...gnu` | GNU/Itanium | `msvcrt.dll` 或 UCRT | `libstdc++` / `libc++` | 同 MinGW |
| Cygwin | `gcc` | Cygwin ABI | Cygwin libc | `libstdc++` | `cygwin1.dll` |

---

## 3. 关键区别

### MSVC

- 微软官方 Windows 编译器。
- 使用 **UCRT + VCRuntime + MSVC STL**。
- 部署通常依赖 **VC++ Redistributable**。
- 典型 DLL：`vcruntime140.dll`、`msvcp140.dll`、`ucrtbase.dll`。

### MinGW-w64

- Windows 上的 GCC 工具链，主流是 **MinGW-w64**。
- 生成原生 Windows 程序。
- C 运行时常用 `msvcrt.dll`，也可选 UCRT。
- C++ 标准库是 `libstdc++`。
- C++ ABI 与 MSVC **不兼容**。

### Clang

- 本身只是编译器前端，不绑定 C++ 库。
- Windows 上常见两种模式：
  - **MSVC 模式**：`clang-cl`，兼容 MSVC ABI / STL。
  - **MinGW 模式**：`clang --target=...gnu`，通常兼容 MinGW/GCC 生态。

### C 库、C++ 库、VC++ 库

- **C 库**：`msvcrt.dll`、UCRT。
- **C++ 库**：MSVC STL、`libstdc++`、`libc++`。
- **VC++ 库 / VC++ 运行库**：通常指 **VC++ Redist**，包含 VCRuntime 和 MSVC STL 相关 DLL。

---

## 4. 兼容性重点

- C ABI 相对稳定，但要注意调用约定、符号修饰、CRT 边界。
- C++ ABI：**MSVC 与 MinGW/GCC 不兼容**。
- MSVC 的 C++ DLL 不能直接给 MinGW 用，反之亦然。
- `clang-cl` 可与 MSVC 混用。
- Clang 的 MinGW 模式可与 GCC MinGW 混用，但要保持 ABI、STL、运行时一致。
- 跨编译器插件 / DLL 推荐导出 **C 接口**。
- 不要混用 `/MT` 和 `/MD`。
- 不要跨 CRT 堆释放内存，例如一个模块 `malloc`，另一个模块 `free`。

---

## 5. 部署依赖

### MSVC 程序

- `/MD`：安装 **VC++ Redistributable**。
- `/MT`：静态链接 CRT，不依赖 VC++ Redist，但体积更大。
- 常见依赖：
  - `vcruntime140.dll`
  - `msvcp140.dll`
  - `ucrtbase.dll`

### MinGW 程序

常见需要复制：

- `libstdc++-6.dll`
- `libgcc_s_seh-1.dll` 或 `libgcc_s_dw2-1.dll`
- `libwinpthread-1.dll`

也可静态链接：

```bash
-static-libgcc -static-libstdc++ -static
```

### Clang 程序

- `clang-cl`：按 MSVC 方式部署。
- `clang --target=...gnu`：按 MinGW 方式部署。

---

## 6. 选择建议

| 场景 | 建议 |
|---|---|
| Visual Studio 生态 | MSVC 或 `clang-cl` |
| 想用 GCC | MinGW-w64 / MSYS2 |
| 想用 Clang 且兼容 MSVC | `clang-cl` |
| 想用 Clang 且兼容 MinGW/GCC | `clang --target=...gnu` |
| 跨编译器 DLL / 插件 | 导出 C 接口 |
| MSVC 与 MinGW 混用 | 不要混用 C++ 对象 |
| CRT 链接方式 | 不要混用 `/MT` 与 `/MD` |
| 内存释放 | 不要跨 CRT 堆释放 |

---

## 7. 一句话总结

**MSVC 和 MinGW 是两套 Windows 原生 C++ 生态，ABI 不兼容；Clang 可分别接入这两套。C 库看 `msvcrt`/UCRT，C++ 库看 MSVC STL/`libstdc++`，VC++ 库通常指 VC++ Redist。跨编译器优先使用 C ABI。**