cmake_minimum_required(VERSION 3.1)

project(MVE)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED yes)

# Compiler flags
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    add_compile_options(/W3)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    add_compile_options(-Wall -Wextra -Wundef -pedantic)
    if(NOT (${CMAKE_SYSTEM_PROCESSOR} STREQUAL "aarch64" OR ${CMAKE_SYSTEM_PROCESSOR} STREQUAL "arm64") )
        add_compile_options(-mpopcnt)
    endif()
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    add_compile_options(-Weverything -pedantic)
endif()

if(WIN32)
    add_definitions(-DWIN32_LEAN_AND_MEAN -DVC_EXTRALEAN -DNOMINMAX)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    if(MSVC)
        add_compile_options(/MP)
        option(MVE_BUILD_WITH_SSE2 "Build MVE with SSE2 support" ON)
        option(MVE_BUILD_WITH_SSE3 "Build MVE with SSE3 support" ON)
        if(MVE_BUILD_WITH_SSE2)
            add_definitions(-D__SSE2__)
        endif()
        if(MVE_BUILD_WITH_SSE3)
            add_definitions(-D__SSE3__)
        endif()
    endif()
endif()

find_package(OpenMP REQUIRED)
add_compile_options(${OpenMP_CXX_FLAGS})

include_directories(libs)

add_subdirectory(libs)
#add_subdirectory(apps)
#add_subdirectory(tests)
