# cloc

English | [简体中文](README.md)

**Count source lines in a project directory from the command line.**

**Positioning**: Developers who need a total source-line count for a project directory, typically for software copyright registration forms or project-size surveys, on Windows / Linux / macOS.
**Status**: v0.1.0 released (evidence: [Releases](https://github.com/HestiaLab/cloc/releases/tag/v0.1.0), 2026-07-09; the Release has no binary assets, so build from source; `CMakeLists.txt` declares `project(cloc VERSION 0.1.0)`).

[![CI](https://github.com/HestiaLab/cloc/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/HestiaLab/cloc/actions/workflows/ci.yml)
[![Static Analysis](https://github.com/HestiaLab/cloc/actions/workflows/static-analysis.yml/badge.svg?branch=main)](https://github.com/HestiaLab/cloc/actions/workflows/static-analysis.yml)
[![Release](https://img.shields.io/github/v/release/HestiaLab/cloc)](https://github.com/HestiaLab/cloc/releases)
[![License](https://img.shields.io/github/license/HestiaLab/cloc)](LICENSE)

[Command line usage](#command-line-usage) · [Quick start](#quick-start) · [Documentation](doc/README.md) · [Releases](https://github.com/HestiaLab/cloc/releases) · [简体中文](README.md)

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
(The `Project:` line is the absolute path you pass; see the sample layout below. Output labels are English.)

Sample directory `C:/hestialab-verify/cloc/sample`:

```text
sample/
├─ NOTES.md                    # not source; not counted
├─ src/
│  ├─ add.c
│  ├─ add.h
│  ├─ app.js
│  └─ main.c
├─ build/gen.c                 # build/ is skipped
└─ node_modules/pkg/index.js   # node_modules/ is skipped
```

`Visited files: 5` is `NOTES.md` plus the four source files; `Matched source files: 4`; `Skipped directories: 2` are `build/` and `node_modules/`.

cloc is a C11 command-line tool for counting source lines in a project directory. It counts physical lines, including comments and blank lines, and counts a final line even when the file does not end with a newline. It automatically skips 16 common generated or build directories plus any `cmake-build-*` prefix directory.
Built-in presets cover `common`, `c`, `cpp`, `web`, and `dotnet` (`common` covers 36 suffixes), and custom suffixes are accepted directly. The core is also a reusable C11 library consumable through `find_package(cloc)`.

## Command Line Usage

Build the project, then run:

```sh
cloc <absolute-project-path> [--preset <name>]
cloc <absolute-project-path> [suffix...]
cloc --interactive
cloc --list-presets
cloc --help
```

Examples (paths below are placeholders; replace them with your own absolute paths):

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project
```

Use a preset for a project type:

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project --preset web
```

Run interactively:

```powershell
.\build\ninja-debug\bin\cloc.exe --interactive
```

Show help:

```powershell
.\build\ninja-debug\bin\cloc.exe --help
```

Pass suffixes directly:

```powershell
.\build\ninja-debug\bin\cloc.exe C:\work\my-project "*.c" "*.h" "*.js" "*.ts" "*.cs" "*.razor" "*.css"
```

```sh
./build/ninja-debug/bin/cloc /home/me/my-project c h cpp js ts cs razor css
```

List available presets:

```powershell
.\build\ninja-debug\bin\cloc.exe --list-presets
```

Built-in presets:

| Preset | Use case |
| --- | --- |
| `common` | Default. Common code suffixes across mainstream project types. |
| `c` | C source and header files. |
| `cpp` | C and C++ source/header files. |
| `web` | JavaScript/JSX, TypeScript/TSX, CSS/SCSS/Sass, HTML/HTM, Vue, and Svelte files. |
| `dotnet` | C#, Razor, CSHTML, and XAML files. |

Suffixes may be written as `*.js`, `.js`, or `js`. If no suffixes or preset are provided, the tool uses the `common` preset.

Interactive mode asks for the project absolute path, then lets the user choose a preset by number/name or enter a custom suffix list separated by spaces, commas, or semicolons.

The tool counts physical lines, including comments and blank lines, and counts a final line even when the file does not end with a newline. Common generated or build directories are skipped. The full list is `.git`, `.hg`, `.svn`, `.vs`, `.idea`, `build`, `out`, `dist`, `install`, `node_modules`, `bin`, `obj`, `target`, `.next`, `.nuxt`, `coverage`, plus every `cmake-build-*` prefix directory (source: `src/cloc/line_counter.c:34-55`).

> The CLI currently has no `--version`; use `CMakeLists.txt` and the Release for the version. Output labels are English.

## Publish

Create a release build and copy the command-line executable to an output directory:

**Windows PowerShell**

```powershell
.\scripts\publish.ps1 C:\tools\cloc
```

**Linux/macOS or MSYS2/Git Bash**

```sh
./scripts/publish.sh /opt/cloc
```

If no output directory is provided, the executable is copied to the repository root. The scripts use the `ninja-release` preset by default and accept `--preset <name>` when another preset is needed. On non-Windows platforms, the published executable does not use the `.exe` suffix.

## Development

- CMake modules split by responsibility.
- C11 as the target-level language baseline.
- Cross-platform compiler support for MSVC, GCC, Clang, and AppleClang.
- CMake presets for debug, release, sanitizer, and coverage builds.
- Static and shared library builds through `BUILD_SHARED_LIBS`.
- CTest based unit tests.
- Installable CMake package config with downstream smoke tests.
- `add_subdirectory()` smoke tests for subproject consumers.
- GitHub Actions for build, test, and static analysis.
- Short contributor, testing, release, security, and AI-agent guidance.
- Focused public C APIs for source-line counting and source suffix presets.

## Layout

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

## Quick Start

Required tools:

- CMake 3.21 or newer.
- A C11 compiler from MSVC, GCC, Clang, or AppleClang.
- Ninja for the default cross-platform presets, or another CMake-supported generator through local presets.

On Windows, run the Ninja presets from a Developer PowerShell, Developer Command Prompt, or VS Code CMake kit that initializes MSVC. This audit was verified with the MSYS2 UCRT64 GCC 16.2.0 + CMake 4.3.3 + Ninja 1.13.2 combination.

For step-by-step installation instructions per platform, see [doc/guides/environment.md](doc/guides/environment.md).

Recommended editor tools:

- VS Code with the CMake Tools and clangd extensions.
- Ninja, because it generates `compile_commands.json` for clangd.
- Microsoft C/C++ remains usable as a local fallback; see `.vscode/c_cpp_properties.example.json`.

The repository includes portable VS Code recommendations, CMake tasks, clangd defaults, an optional C/C++ fallback configuration, and Ninja presets. Compiler installation paths and per-user kits stay in local user configuration.

First-time setup:

**Windows PowerShell**

```powershell
./scripts/bootstrap.ps1
```

**Linux/macOS**

```sh
./scripts/bootstrap.sh
```

The bootstrap script checks required tools and runs configure so editors can load `build/ninja-debug/compile_commands.json`.

Equivalent direct commands (verified on this machine):

```sh
cmake --preset ninja-debug
cmake --build --preset ninja-debug
ctest --preset ninja-debug --output-on-failure
```

Local result: configure/build succeeded and CTest passed **3/3** (`cloc_line_counter_tests`, `cloc_source_preset_tests`, `cloc_cli_options_tests`).

For a local Visual Studio, Xcode, or Make workflow, copy `CMakeUserPresets.example.json` to `CMakeUserPresets.json` and edit it for your machine. The real user preset file is ignored by Git.

Daily check:

**Windows PowerShell**

```powershell
./scripts/check.ps1
```

**Linux/macOS**

```sh
./scripts/check.sh
```

Optional clang-tidy checks:

**Windows PowerShell**

```powershell
./scripts/check.ps1 -EnableTidy
```

**Linux/macOS**

```sh
./scripts/check.sh --enable-tidy
```

For a fuller explanation of configure, build, test, install, presets, and common errors, see [doc/guides/cmake.md](doc/guides/cmake.md).

## Documentation Map

- [doc/README.md](doc/README.md) maps each topic to its source of truth.
- [doc/guides/cmake.md](doc/guides/cmake.md) explains day-to-day CMake usage.
- [doc/guides/testing.md](doc/guides/testing.md) describes test and static-analysis expectations.
- [CONTRIBUTING.md](CONTRIBUTING.md) describes branch, commit, and PR workflow.
- [SECURITY.md](SECURITY.md) describes vulnerability and dependency handling.
- [ENGINEERING_STANDARD.md](ENGINEERING_STANDARD.md) defines the project engineering baseline.

## Installed Package Use

The install rules generate a CMake package config:

```cmake
find_package(cloc CONFIG REQUIRED)
target_link_libraries(app PRIVATE cloc::cloc_core)
```

The `tests/package_smoke/` project validates installed C and C++ consumers.

Direct source-tree consumption through `add_subdirectory()` is validated by `tests/subproject_smoke/`.

> Note: the install rules only install the `cloc_core` library (`cmake/Packaging.cmake:6`); the `cloc` executable is **not** installed. Copy the command-line program with the Publish section above.

## License

cloc is released under the [MIT License](LICENSE).
