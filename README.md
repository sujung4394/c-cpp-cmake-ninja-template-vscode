# C/C++ CMake(Ninja) VSCode 템플릿

Windows + VSCode에서 C/C++ 프로젝트를 **경로 설정 없이 바로** 시작하기 위한 템플릿입니다.
clone 후 F5만 누르면 빌드와 디버깅이 됩니다.

## 요구 사항

- MSYS2 UCRT64 툴체인 (GCC, G++, GDB), CMake 3.20+, Ninja
- **설치 방법은 [INSTALL.md](INSTALL.md)를 따라 주세요.**

`C:\msys64\ucrt64\bin`이 시스템 Path에 등록되어 있어야 합니다.
이 템플릿은 절대경로를 하드코딩하지 않고 Path에서 도구를 찾습니다.

## 시작하기

```bash
git clone <이 저장소 주소> my-project
cd my-project
code .
```

VSCode가 확장 설치를 안내하면 수락하세요. 그다음 할 일은 두 가지뿐입니다.

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

## 문제 해결

| 증상 | 해결 |
|---|---|
| `ninja를 찾을 수 없음`, `gcc를 찾을 수 없음` | Path 등록 후 VSCode를 완전히 종료했다가 다시 여세요 |
| IntelliSense가 빨간 줄만 표시 | 한 번 빌드하면 `build/compile_commands.json`이 생기면서 해결됩니다 |
| F5를 눌렀는데 `debug/main.exe` 없음 | 빌드 실패입니다. 터미널 패널의 컴파일 에러를 확인하세요 |
| 디버깅 중 `std::cin`에 입력이 안 됨 | 통합 터미널 탭에 직접 입력하세요 (`console: integratedTerminal`) |
| 소스를 추가했는데 빌드에 안 들어감 | `build/` 폴더를 지우고 다시 빌드하세요 |
