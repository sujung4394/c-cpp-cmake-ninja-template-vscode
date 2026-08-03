# 요구 프로그램 설치 (Windows)

Windows에서 GCC/GDB 툴체인을 갖추는 가장 간단한 방법은 MSYS2입니다.
아래 순서대로 한 번만 진행하면 됩니다.

## 1. Git 설치

[git-scm.com](https://git-scm.com/download/win)에서 설치합니다.

## 2. VSCode 설치

[code.visualstudio.com](https://code.visualstudio.com/)에서 설치합니다.
확장은 따로 찾지 않아도 됩니다. 이 저장소를 열면 필요한 확장(C/C++, CMake Tools)을
설치하라는 알림이 자동으로 뜹니다.

## 3. MSYS2 설치

[msys2.org](https://www.msys2.org/)에서 설치합니다. 기본 경로는 `C:\msys64`입니다.

## 4. 툴체인 설치

시작 메뉴에서 **MSYS2 UCRT64** 터미널을 실행합니다.
(MSYS2 MSYS나 MINGW64가 아니라 **UCRT64**여야 합니다.)

```bash
# 패키지 저장소 업데이트
# 도중에 터미널이 닫힐 수 있습니다. 다시 열고 한 번 더 실행하세요.
pacman -Syu

# GCC, G++, GDB, CMake, Ninja를 한 번에 설치
pacman -S --needed \
    mingw-w64-ucrt-x86_64-toolchain \
    mingw-w64-ucrt-x86_64-cmake \
    mingw-w64-ucrt-x86_64-ninja
```

`toolchain` 그룹은 설치할 항목을 물어봅니다. 그냥 Enter를 누르면 전체가 설치됩니다.

> Ninja를 쓰므로 `make`는 설치하지 않아도 됩니다.

## 5. 환경 변수 Path 등록 — **가장 중요합니다**

이 단계를 건너뛰면 이 템플릿이 동작하지 않습니다.

1. `Win + R` → `sysdm.cpl` → **고급** 탭 → **환경 변수**
2. **시스템 변수** 목록에서 `Path` 선택 → **편집** → **새로 만들기**
3. 다음 경로를 추가:

   ```
   C:\msys64\ucrt64\bin
   ```

   (MSYS2를 다른 곳에 설치했다면 그에 맞게 바꾸세요.)

## 6. 확인

**새** 명령 프롬프트 또는 PowerShell을 열고(기존 창은 Path가 반영되지 않습니다):

```powershell
gcc --version
g++ --version
gdb --version
cmake --version
ninja --version
```

다섯 개 모두 버전이 출력되면 준비가 끝났습니다. CMake는 3.20 이상이어야 합니다.

## 자주 겪는 문제

| 증상 | 원인 |
|------|------|
| `gcc는 인식할 수 없는 명령` | 5번 Path 등록을 안 했거나, 등록 전에 열어둔 터미널/VSCode를 그대로 쓰고 있음 |
| `pacman: command not found` | 일반 명령 프롬프트에서 실행함. MSYS2 UCRT64 터미널을 사용해야 함 |
| 설치는 됐는데 VSCode에서만 못 찾음 | Path 등록 후 VSCode를 **완전히 종료 후 재실행**해야 반영됨 |
