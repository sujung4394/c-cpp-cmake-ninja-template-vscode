cmake_minimum_required(VERSION 3.20)

# IntelliSense가 실제 빌드 플래그를 그대로 읽도록 컴파일 DB를 생성합니다.
# build/compile_commands.json 이 만들어지며 c_cpp_properties.json 이 이를 참조합니다.
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# PROJECT_NAME 자리에 실제 프로젝트 이름을 넣으세요.
project(PROJECT_NAME LANGUAGES C CXX)

set(CMAKE_C_STANDARD 17)
set(CMAKE_C_STANDARD_REQUIRED ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# --- 실행 파일 출력 위치 -------------------------------------------------
# Ninja Multi-Config는 config별 변수를 지정하지 않으면 bin/Release/ 처럼
# 하위 폴더를 하나 더 만듭니다. 네 가지 config를 모두 명시해 둡니다.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG          ${CMAKE_SOURCE_DIR}/debug)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE        ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_MINSIZEREL     ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY                ${CMAKE_SOURCE_DIR}/bin)

# --- 소스 수집 -----------------------------------------------------------
# CONFIGURE_DEPENDS 덕분에 src/ 에 파일을 추가·삭제해도 자동으로 다시 구성됩니다.
file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS
    src/*.c   src/*.cc  src/*.cpp src/*.cxx
    src/*.h   src/*.hh  src/*.hpp src/*.hxx
)

# 실행 파일 이름은 main 으로 고정입니다.
# (.vscode 의 tasks.json / launch.json 이 main.exe 경로를 참조합니다.
#  이름을 바꾸려면 두 파일의 경로도 함께 수정하세요.)
add_executable(main ${SOURCES})

# src/ 를 include 루트로 잡아 #include "foo/bar.hpp" 형태를 쓸 수 있게 합니다.
target_include_directories(main PRIVATE ${CMAKE_SOURCE_DIR}/src)

# 경고를 켜고 싶다면 아래 주석을 해제하세요. 초보자의 실수를 상당수 잡아줍니다.
# target_compile_options(main PRIVATE -Wall -Wextra)
