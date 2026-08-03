# C/C++ CMake(Ninja) 기본 프로젝트 세팅

## 요구 사항

- 요구 프로그램 설치 방법.md를 참고하세요
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

경로를 채운 후, 주석을 해제하여 git이 이 파일들을 무시하도록 설정합니다.
### `.gitignore`
```
.vscode
```

PROJECT_NAME에 사용하고자 하는 프로젝트의 이름을 넣습니다. 

### `CMakeLists.txt`

```cmake
project(PROJECT_NAME LANGUAGES CXX C)
```

## 빌드

`Ctrl+Shift+P` → **CMake: Configure** 실행 후:

| 단축키 | 동작 |
|--------|------|
| F7 | Release 빌드 → `bin/main.exe` |
| F5 | Debug 빌드 → `debug/main.exe` → gdb 디버그 |

## 실행

지금은 tasks.json과 launch.json이 1개의 인자를 받을 수 있도록 예시로 만들어져 있습니다. 
인자를 받지 않는 경우에는 `args`, `inputs`를 삭제하세요. 
인자를 여러개 받는 경우에는 아래와 같은 방식으로 수정하세요. 

### `.vscode/tasks.json` `.vscode/launch.json`

```json
"args": ["${input:arg1}", "${input:arg2}"],

"inputs": [
    {
        "id": "arg1",
        "type": "promptString",
        "description": "입력 파일 경로"
    },
    {
        "id": "arg2",
        "type": "promptString",
        "description": "출력 파일 경로"
    }
],
```
