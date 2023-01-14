cmake_minimum_required(VERSION 3.0)

project(signature_analyser VERSION 0.1.0 LANGUAGES CXX)
set(CMAKE_CXX_FLAGS "")
set(CMAKE_CXX_FLAGS_DEBUG "")
set(CMAKE_CXX_FLAGS_RELEASE "")

string(APPEND CMAKE_CXX_FLAGS " -Wall -Wextra -pedantic")
string(APPEND CMAKE_CXX_FLAGS " -Wconversion -Wunreachable-code -Wuninitialized -Wold-style-cast")
string(APPEND CMAKE_CXX_FLAGS " -Wctor-dtor-privacy -Wnon-virtual-dtor -Wconversion -Wsign-conversion -Winit-self")
string(APPEND CMAKE_CXX_FLAGS " -Wunreachable-code -Wfloat-equal")

string(APPEND CMAKE_CXX_FLAGS " -fPIC")

string(APPEND CMAKE_CXX_FLAGS_DEBUG " -g -Og")
string(APPEND CMAKE_CXX_FLAGS_RELEASE " -O3 -Werror")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_CXX_EXTENSIONS OFF)

Include(FetchContent)

FetchContent_Declare(
        Catch2
        GIT_REPOSITORY https://github.com/catchorg/Catch2.git
        GIT_TAG v3.0.1 # or a later release
)

FetchContent_MakeAvailable(Catch2)

add_executable(signature_analyser
        cli/main.cpp
        )
