cmake_minimum_required(VERSION 3.16)

project(spirv-reflect)

OPTION(SPIRV_REFLECT_ENABLE_ASAN    "Use address sanitization" OFF)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CMAKE_CXX_STANDARD 14)

if (SPIRV_REFLECT_ENABLE_ASAN)
    add_compile_options(-fsanitize=address -fno-omit-frame-pointer)
    add_link_options(-fsanitize=address)
endif()

add_library(spirv-reflect-c-shared SHARED
${CMAKE_CURRENT_SOURCE_DIR}/SPIRV-Reflect/spirv_reflect.h
${CMAKE_CURRENT_SOURCE_DIR}/SPIRV-Reflect/spirv_reflect.c
                                 )

target_include_directories(spirv-reflect-c-shared
                           PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
install(TARGETS spirv-reflect-c-shared
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)

if (MSVC)
    # Windows with MSVC: Automatically export all symbols
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
elseif (CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    # Unix-like systems with GCC or Clang: Set visibility to default
    target_compile_options(spirv-reflect-c-shared PRIVATE -fvisibility=default)
endif()