cmake_minimum_required(VERSION 3.20)
project(BWT LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_SOURCE_DIR}/debug)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

file(GLOB_RECURSE SOURCES
    src/*.c src/*.m
    src/*.C src/*.M src/*.c++ src/*.cc src/*.cpp src/*.cxx src/*.mm
    src/*.h src/*.hh src/*.h++ src/*.hm src/*.hpp src/*.hxx src/*.in src/*.txx
)
add_executable(main ${SOURCES})
