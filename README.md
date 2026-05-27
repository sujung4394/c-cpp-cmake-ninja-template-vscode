# C/C++ CMake(Ninja) 기본 프로젝트 세팅

## 요구 사항

- [CMake](https://cmake.org/) 3.20 이상
- MinGW (GCC, G++, GDB)
- [Ninja](https://ninja-build.org/)

## 최초 설정 (clone 또는 pull 후)

아래 세 파일에 로컬 환경의 실제 경로를 채워 넣어야 합니다.

### `.vscode/settings.json`

```json
"-DCMAKE_MAKE_PROGRAM=<ninja.exe 경로>",
"-DCMAKE_C_COMPILER=<gcc.exe 경로>",
"-DCMAKE_CXX_COMPILER=<g++.exe 경로>"
```

### `.vscode/launch.json`

```json
"miDebuggerPath": "<gdb.exe 경로>"
```

### `.vscode/c_cpp_properties.json`

```json
"compilerPath": "<g++.exe 경로>"
```

경로를 채운 후, 아래 명령으로 git이 이 파일들의 변경을 무시하도록 설정합니다.

```bash
git update-index --skip-worktree .vscode/settings.json
git update-index --skip-worktree .vscode/launch.json
git update-index --skip-worktree .vscode/c_cpp_properties.json
```

## 빌드

`Ctrl+Shift+P` → **CMake: Configure** 실행 후:

| 단축키 | 동작 |
|--------|------|
| F7 | Release 빌드 → `bin/main.exe` |
| F5 | Debug 빌드 → `debug/main.exe` → gdb 디버그 |
