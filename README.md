# C/C++ CMake(Ninja) VSCode 템플릿

Windows + VSCode에서 C/C++ 프로젝트를 **경로 설정 없이 바로** 시작하기 위한 템플릿입니다.
clone 후 F5만 누르면 빌드와 디버깅이 됩니다.

## 요구 사항

- MSYS2 UCRT64 툴체인 (GCC, G++, GDB), CMake 3.20+, Ninja
- **설치 방법은 [docs/INSTALL.md](docs/INSTALL.md)를 따라 주세요.**

`C:\msys64\ucrt64\bin`이 시스템 Path에 등록되어 있어야 합니다.
이 템플릿은 절대경로를 하드코딩하지 않고 Path에서 도구를 찾습니다.

## 시작하기

```bash
git clone <이 저장소 주소> my-project
cd my-project
code .
```

VSCode가 확장 설치를 안내하면 수락하세요.

> 이 안내는 **`.vscode/extensions.json`에 적힌 확장이 아직 설치되어 있지 않을 때만**
> 뜹니다. 이미 C/C++ · CMake Tools 확장을 쓰고 계셨다면 팝업 없이 조용히 넘어가는
> 게 정상 동작입니다. 안내가 안 떴다고 확장이 빠진 건 아닌지 걱정되시면 아래
> [확장이 자동으로 설치되지 않을 때](#확장이-자동으로-설치되지-않을-때)를 참고하세요. 그다음 할 일은 두 가지뿐입니다.

1. `CMakeLists.txt`의 `project(PROJECT_NAME ...)`에 프로젝트 이름 입력
2. `src/main.cpp` 작성

## 빌드와 실행

| 단축키 / 명령 | 동작 | 결과물 |
|---|---|---|
| `Ctrl+Shift+B` | Release 빌드 | `bin/main.exe` |
| `F5` | Debug 빌드 후 gdb 디버깅 | `debug/main.exe` |
| `Ctrl+Shift+P` → `Run Test Task` | 빌드 후 실행 | `bin/main.exe` 실행 |

CMake 구성(configure)은 빌드 태스크가 알아서 먼저 실행하므로 따로 누를 필요가 없습니다.
`build/` 폴더를 지워도 다음 빌드에서 자동으로 다시 만들어집니다.

> **F7은 쓰지 마세요.** F7은 CMake Tools 확장의 자체 빌드 명령이라
> 상태바에 선택된 variant(기본값 Debug)를 따라가므로, 어떤 결과물이 나올지
> 상황에 따라 달라집니다. 위 표의 방식을 쓰시면 항상 일관됩니다.

## 실행 인자 넘기기

인자가 필요 없으면 아무것도 안 하셔도 됩니다. 기본 실행/디버그는 인자를 묻지 않습니다.

인자가 필요할 때:

- 실행: `Ctrl+Shift+P` → `Run Task` → **실행 (인자 입력)**
- 디버깅: 실행 및 디버그 패널에서 **디버그 (인자 입력)** 구성 선택 후 F5

인자를 여러 개 받으려면 `tasks.json`과 `launch.json`을 이렇게 늘리세요.

```jsonc
"args": ["${input:arg1}", "${input:arg2}"],

"inputs": [
    { "id": "arg1", "type": "promptString", "description": "입력 파일 경로" },
    { "id": "arg2", "type": "promptString", "description": "출력 파일 경로" }
]
```

## 프로젝트 구조

```
├─ .vscode/
│  ├─ c_cpp_properties.json  IntelliSense (compile_commands.json 연동)
│  ├─ extensions.json        권장 확장 목록
│  ├─ launch.json            F5 디버깅 구성
│  ├─ settings.json          CMake 제너레이터 / 컴파일러
│  └─ tasks.json             구성 · 빌드 · 실행 태스크
├─ docs/INSTALL.md           툴체인 설치 가이드
├─ src/main.cpp              소스는 전부 이 폴더 안에
├─ CMakeLists.txt
├─ bin/                      Release 결과물 (git 추적 제외)
├─ debug/                    Debug 결과물 (git 추적 제외)
└─ build/                    CMake 작업 폴더 (git 추적 제외)
```

`src/` 아래에 파일을 추가하면 `CMakeLists.txt`를 고치지 않아도 자동으로 빌드에 포함됩니다
(`GLOB_RECURSE ... CONFIGURE_DEPENDS`). 하위 폴더도 됩니다.

`src/`가 include 루트이므로 `src/util/math.hpp`는 `#include "util/math.hpp"`로 부를 수 있습니다.

## 알아두면 좋은 것

- **실행 파일 이름은 항상 `main.exe`입니다.** 프로젝트 이름과 무관합니다.
  바꾸려면 `CMakeLists.txt`의 `add_executable(main ...)`과 함께
  `tasks.json` · `launch.json`의 `main.exe` 경로도 수정해야 합니다.
- **빌드 config는 Debug와 Release만 쓰세요.** RelWithDebInfo 등은 출력 경로가 달라져
  실행 태스크가 파일을 못 찾습니다.
- 경고를 켜려면 `CMakeLists.txt` 맨 아래 `target_compile_options(main PRIVATE -Wall -Wextra)`
  주석을 해제하세요.
- `.vscode` 폴더는 의도적으로 커밋합니다. 이 템플릿의 본체가 그 안에 있기 때문입니다.
  개인 설정을 분리하고 싶다면 `.gitignore` 하단의 주석 처리된 블록을 참고하세요.
- **"실행" 태스크는 기본적으로 빌드까지 함께 실행합니다.** `tasks.json`의 "실행"
  태스크에 `"dependsOn": "Release 빌드"`가 걸려 있어서, 실행할 때마다 먼저
  최신 코드로 다시 빌드한 뒤 실행하는 구조입니다. 코드를 안 고쳤는데도
  매번 재빌드되는 게 번거롭다면(예: 같은 실행 파일을 인자만 바꿔 여러 번
  돌려볼 때) 해당 `dependsOn` 줄을 지우거나 주석 처리하세요. 그러면 "실행"
  태스크가 빌드를 건너뛰고 `bin/main.exe`를 바로 실행하므로, 빌드와 실행이
  분리됩니다. 다만 이 경우 코드를 고친 뒤에는 **빌드를 직접 한 번
  실행**해 주셔야 최신 결과물이 반영됩니다.

## 확장이 자동으로 설치되지 않을 때

`.vscode/extensions.json`의 `recommendations` 목록은 두 조건을 모두 만족할 때만 팝업을 띄웁니다.

1. 목록에 적힌 확장이 **아직 설치되어 있지 않을 것**
2. 이 워크스페이스에서 그 안내를 **한 번도 닫거나 무시한 적 없을 것**

**이미 확장이 설치되어 있어서 안 뜨는 경우** — 정상입니다. 직접 확인하려면
`Ctrl+Shift+X` → 검색창에 `@recommended` 입력 → **Workspace Recommendations**에
`C/C++`, `CMake Tools`, `CMake`가 보이는지 확인하세요.

**예전에 팝업을 닫아버려서(Don't Show Again) 다시 안 뜨는 경우** — 아래로 초기화합니다.

```
Ctrl+Shift+P → Preferences: Open User Settings (JSON)
```

`"extensions.ignoreRecommendations": true`가 있으면 지우거나 `false`로 바꾸세요.
그래도 안 뜨면 위의 `@recommended` 화면에서 확장별 **⋯ → Install Workspace
Recommended Extensions**을 눌러 수동으로 설치할 수 있습니다.

## GCC가 여러 버전 설치되어 있을 때

MinGW를 여러 경로에 설치했거나, Path에 여러 GCC 배포판(MSYS2, WinLibs, 다른
MinGW 등)이 동시에 등록되어 있으면 `gcc`라는 이름만으로는 어떤 것이 잡힐지
알 수 없습니다. Path 순서상 앞에 있는 게 우선하는데, 그게 원하는 버전이
아닐 수 있습니다.

이럴 때는 이름 대신 **절대경로**를 직접 적어서 특정 실행 파일을 고정하세요.
수정할 곳은 네 파일입니다.

**`.vscode/settings.json`**

```jsonc
"cmake.configureArgs": [
    "-DCMAKE_MAKE_PROGRAM=C:/msys64/ucrt64/bin/ninja.exe",
    "-DCMAKE_C_COMPILER=C:/msys64/ucrt64/bin/gcc.exe",
    "-DCMAKE_CXX_COMPILER=C:/msys64/ucrt64/bin/g++.exe"
]
```

**`.vscode/tasks.json`** — "CMake 구성" 태스크의 `args`도 동일하게 맞춰야 두 설정이
어긋나지 않습니다.

```jsonc
"args": [
    "-S", "${workspaceFolder}",
    "-B", "${workspaceFolder}/build",
    "-G", "Ninja Multi-Config",
    "-DCMAKE_MAKE_PROGRAM=C:/msys64/ucrt64/bin/ninja.exe",
    "-DCMAKE_C_COMPILER=C:/msys64/ucrt64/bin/gcc.exe",
    "-DCMAKE_CXX_COMPILER=C:/msys64/ucrt64/bin/g++.exe"
]
```

**`.vscode/launch.json`** — `miDebuggerPath`도 같은 방식으로 고정합니다.

```jsonc
"miDebuggerPath": "C:/msys64/ucrt64/bin/gdb.exe"
```

**`.vscode/c_cpp_properties.json`** — `compilerPath`도 함께 맞춰야 IntelliSense가
실제 빌드에 쓰는 컴파일러와 다른 버전 헤더를 보는 일이 없습니다.

```jsonc
"compilerPath": "C:/msys64/ucrt64/bin/g++.exe"
```

> Windows 경로에서 백슬래시(`\`)를 쓰려면 `\\`로 이스케이프해야 하므로,
> JSON 안에서는 위 예시처럼 슬래시(`/`)를 쓰는 편이 훨씬 편합니다. CMake와
> VSCode 모두 슬래시 경로를 문제없이 인식합니다.

이렇게 절대경로로 고정하면 이 프로젝트는 Path에 무엇이 몇 개 등록되어 있든
항상 지정한 그 실행 파일만 사용합니다. 다만 이 경로는 개발자마다 설치
위치가 다를 수 있으니, 여러 사람이 함께 쓰는 저장소라면 `.gitignore`에서
`.vscode`를 무시하도록 바꾸고 각자 로컬에서 채우는 방식을 고려하세요.

## 문제 해결

| 증상 | 해결 |
|---|---|
| `ninja를 찾을 수 없음`, `gcc를 찾을 수 없음` | Path 등록 후 VSCode를 완전히 종료했다가 다시 여세요 |
| IntelliSense가 빨간 줄만 표시 | 한 번 빌드하면 `build/compile_commands.json`이 생기면서 해결됩니다 |
| F5를 눌렀는데 `debug/main.exe` 없음 | 빌드 실패입니다. 터미널 패널의 컴파일 에러를 확인하세요 |
| 디버깅 중 `std::cin`에 입력이 안 됨 | 통합 터미널 탭을 클릭해 포커스를 준 뒤 입력하세요. 그래도 안 되면 `launch.json`에서 해당 구성의 `externalConsole`을 `true`로 바꿔 별도 콘솔 창을 띄워보세요 |
| 소스를 추가했는데 빌드에 안 들어감 | `build/` 폴더를 지우고 다시 빌드하세요 |
| 의도한 것과 다른 GCC/컴파일러가 잡힘 | GCC가 여러 버전 설치된 환경입니다. [GCC가 여러 버전 설치되어 있을 때](#gcc가-여러-버전-설치되어-있을-때) 참고 |
