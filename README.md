# cloc

[English](README.en.md) | 简体中文

**Count source lines in a project directory from the command line.** · 统计项目目录源码行数的 C11 命令行工具。

**定位**：需要给一个项目目录出具总源码行数的开发者，典型场景是软件著作权登记表与项目规模盘点；在 Windows / Linux / macOS 命令行运行。
**状态**：已发布 v0.1.0（依据：[Releases](https://github.com/HestiaLab/cloc/releases/tag/v0.1.0)，2026-07-09；Release 无二进制附件，需从源码构建；`CMakeLists.txt` 的 `project(cloc VERSION 0.1.0)` 与之一致）。

[![CI](https://github.com/HestiaLab/cloc/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/HestiaLab/cloc/actions/workflows/ci.yml)
[![Static Analysis](https://github.com/HestiaLab/cloc/actions/workflows/static-analysis.yml/badge.svg?branch=main)](https://github.com/HestiaLab/cloc/actions/workflows/static-analysis.yml)
[![Release](https://img.shields.io/github/v/release/HestiaLab/cloc)](https://github.com/HestiaLab/cloc/releases)
[![License](https://img.shields.io/github/license/HestiaLab/cloc)](LICENSE)

[命令行用法](#命令行用法) · [快速开始](#快速开始) · [文档](doc/README.md) · [Releases](https://github.com/HestiaLab/cloc/releases) · [English](README.en.md)

```console
$ cloc.exe C:/hestialab-verify/cloc/sample
Project: C:/hestialab-verify/cloc/sample
Selection: common
Suffixes: .c, .h, .cpp, .hpp, .cc, .cxx, .inl, .cs, .razor, .cshtml, .js, .jsx, .ts, .tsx, .css, .scss, .sass, .html, .htm, .vue, .svelte, .java, .kt, .kts, .go, .rs, .py, .php, .rb, .swift, .m, .mm, .sql, .sh, .ps1, .xaml
Total source lines: 11
Matched source files: 4
Visited files: 5
Skipped directories: 2
```
（`Project:` 行为你传入的绝对路径；示例目录见下文。程序输出标签固定为英文。）

示例目录 `C:/hestialab-verify/cloc/sample`：

```text
sample/
├─ NOTES.md                    # 非源码，不计入
├─ src/
│  ├─ add.c
│  ├─ add.h
│  ├─ app.js
│  └─ main.c
├─ build/gen.c                 # build/ 被跳过
└─ node_modules/pkg/index.js   # node_modules/ 被跳过
```

`Visited files: 5` 为 `NOTES.md` + 4 个源码文件；`Matched source files: 4`；`Skipped directories: 2` 为 `build/` 与 `node_modules/`。

cloc 按物理行统计（包括注释与空行，文件末尾无换行也会计入最后一行），并自动跳过 `.git`、`build`、`node_modules` 等 16 个目录与 `cmake-build-*` 前缀目录。
内置 `common` / `c` / `cpp` / `web` / `dotnet` 五组后缀预设（`common` 覆盖 36 种后缀），也可以直接传自定义后缀。核心同时是一个可 `find_package(cloc)` 复用的 C11 库。

## 命令行用法

先构建项目，然后运行：

```sh
cloc <absolute-project-path> [--preset <name>]
cloc <absolute-project-path> [suffix...]
cloc --interactive
cloc --list-presets
cloc --help
```

示例（以下路径为占位，请替换成你自己的绝对路径）：

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project
```

按项目类型使用预设：

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project --preset web
```

交互式运行：

```powershell
.\build\ninja-debug\bin\cloc.exe --interactive
```

查看帮助：

```powershell
.\build\ninja-debug\bin\cloc.exe --help
```

直接指定后缀：

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project "*.c" "*.h" "*.js" "*.ts" "*.cs" "*.razor" "*.css"
```

```sh
./build/ninja-debug/bin/cloc /home/me/my-project c h cpp js ts cs razor css
```

列出可用预设：

```powershell
.\build\ninja-debug\bin\cloc.exe --list-presets
```

内置预设：

| 预设 | 用途 |
| --- | --- |
| `common` | 默认。主流项目类型的常见代码后缀。 |
| `c` | C 源文件与头文件。 |
| `cpp` | C 与 C++ 源文件/头文件。 |
| `web` | JavaScript、TypeScript、CSS、HTML、Vue 与 Svelte 文件。 |
| `dotnet` | C#、Razor、CSHTML 与 XAML 文件。 |

后缀可以写成 `*.js`、`.js` 或 `js`。未提供后缀或预设时，使用 `common` 预设。

交互模式先询问项目绝对路径，然后允许按编号/名称选择预设，或输入用空格、逗号、分号分隔的自定义后缀列表。

工具统计物理行，包含注释与空行；文件末尾没有换行时仍计入最后一行。常见的生成或构建目录会被跳过，完整列表为：`.git`、`.hg`、`.svn`、`.vs`、`.idea`、`build`、`out`、`dist`、`install`、`node_modules`、`bin`、`obj`、`target`、`.next`、`.nuxt`、`coverage`，以及所有 `cmake-build-` 前缀目录（源码见 `src/cloc/line_counter.c:34-55`）。

> CLI 当前没有 `--version`；版本号以 `CMakeLists.txt` 与 Release 为准。工具输出标签固定为英文。

## 发布

创建 release 构建，并把命令行可执行文件复制到输出目录：

**Windows PowerShell**

```powershell
.\scripts\publish.ps1 C:\tools\cloc
```

**Linux/macOS 或 MSYS2/Git Bash**

```sh
./scripts/publish.sh /opt/cloc
```

未提供输出目录时，可执行文件会复制到仓库根目录。脚本默认使用 `ninja-release` 预设；需要其他预设时可传 `--preset <name>`。非 Windows 平台上，发布的可执行文件不带 `.exe` 后缀。

## 开发

- CMake 模块按职责拆分。
- 以 C11 作为目标级语言基线。
- 跨平台编译器支持：MSVC、GCC、Clang、AppleClang。
- CMake 预设覆盖 debug、release、sanitizer 与 coverage 构建。
- 通过 `BUILD_SHARED_LIBS` 支持静态库与共享库构建。
- 基于 CTest 的单元测试。
- 可安装的 CMake 包配置，带下游冒烟测试。
- 面向子项目消费方的 `add_subdirectory()` 冒烟测试。
- GitHub Actions 执行构建、测试与静态分析。
- 面向贡献者、测试、发布、安全与 AI 代理的简短指南。
- 聚焦源码行统计与后缀预设的公共 C API。

## 目录结构

```text
include/        Public headers
src/            Implementation files
tests/          CTest test programs
cmake/          CMake modules
scripts/        Local bootstrap and check entry points
doc/            Short project documentation
doc/guides/     Topic guides
.github/        CI workflows and repository metadata
```

## 快速开始

所需工具：

- CMake 3.21 或更新版本。
- MSVC、GCC、Clang 或 AppleClang 提供的 C11 编译器。
- 默认跨平台预设需要 Ninja；也可以通过本地预设使用其他 CMake 支持的生成器。

在 Windows 上，请从已初始化 MSVC 的 Developer PowerShell、Developer Command Prompt 或 VS Code CMake kit 运行 Ninja 预设。本机实测使用 MSYS2 UCRT64 的 GCC 16.2.0 + CMake 4.3.3 + Ninja 1.13.2 组合。

各平台的逐步安装说明见 [doc/guides/environment.md](doc/guides/environment.md)。

推荐的编辑器工具：

- 安装 CMake Tools 与 clangd 扩展的 VS Code。
- Ninja，因为它会生成供 clangd 使用的 `compile_commands.json`。
- Microsoft C/C++ 仍可作为本地回退方案；见 `.vscode/c_cpp_properties.example.json`。

仓库包含可移植的 VS Code 推荐配置、CMake 任务、clangd 默认值、可选的 C/C++ 回退配置与 Ninja 预设。编译器安装路径与每用户 kit 保留在本地用户配置中。

首次配置：

**Windows PowerShell**

```powershell
./scripts/bootstrap.ps1
```

**Linux/macOS**

```sh
./scripts/bootstrap.sh
```

bootstrap 脚本会检查所需工具并执行 configure，让编辑器可以加载 `build/ninja-debug/compile_commands.json`。

等价的直接命令（本机实测）：

```sh
cmake --preset ninja-debug
cmake --build --preset ninja-debug
ctest --preset ninja-debug --output-on-failure
```

本机结果：configure/build 成功，CTest **3/3 通过**（`cloc_line_counter_tests`、`cloc_source_preset_tests`、`cloc_cli_options_tests`）。

如需本地 Visual Studio、Xcode 或 Make 工作流，把 `CMakeUserPresets.example.json` 复制为 `CMakeUserPresets.json` 并按本机修改。真实的用户预设文件被 Git 忽略。

日常检查：

**Windows PowerShell**

```powershell
./scripts/check.ps1
```

**Linux/macOS**

```sh
./scripts/check.sh
```

可选的 clang-tidy 检查：

**Windows PowerShell**

```powershell
./scripts/check.ps1 -EnableTidy
```

**Linux/macOS**

```sh
./scripts/check.sh --enable-tidy
```

configure、build、test、install、预设与常见错误的更详细说明见 [doc/guides/cmake.md](doc/guides/cmake.md)。

## 文档地图

- [doc/README.md](doc/README.md) 把每个主题映射到其事实来源。
- [doc/guides/cmake.md](doc/guides/cmake.md) 说明日常 CMake 用法。
- [doc/guides/testing.md](doc/guides/testing.md) 描述测试与静态分析要求。
- [CONTRIBUTING.md](CONTRIBUTING.md) 描述分支、提交与 PR 流程。
- [SECURITY.md](SECURITY.md) 描述漏洞与依赖处理。
- [ENGINEERING_STANDARD.md](ENGINEERING_STANDARD.md) 定义项目工程基线。

## 安装包使用

install 规则会生成 CMake 包配置：

```cmake
find_package(cloc CONFIG REQUIRED)
target_link_libraries(app PRIVATE cloc::cloc_core)
```

`tests/package_smoke/` 项目验证安装后的 C 与 C++ 消费方。

通过 `add_subdirectory()` 直接消费源码树的方式由 `tests/subproject_smoke/` 验证。

> 注意：install 规则只安装 `cloc_core` 库（`cmake/Packaging.cmake:6`），**不会**安装 `cloc` 可执行文件；命令行程序请按上文「发布」小节复制。

## 许可证

cloc 以 [MIT 许可证](LICENSE) 发布。
