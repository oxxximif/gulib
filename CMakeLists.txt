cmake_minimum_required(VERSION 3.24)
project(gulib LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

find_package(PkgConfig QUIET)
if(PkgConfig_FOUND)
    pkg_check_modules(SODIUM QUIET libsodium)
endif()

if(NOT SODIUM_FOUND)
    find_path(SODIUM_INCLUDE_DIR sodium.h
        PATHS /ucrt64/include /mingw64/include
        NO_DEFAULT_PATH
    )
    find_library(SODIUM_LIBRARY NAMES sodium libsodium
        PATHS /ucrt64/lib /mingw64/lib
        NO_DEFAULT_PATH
    )
    
    if(SODIUM_INCLUDE_DIR AND SODIUM_LIBRARY)
        set(SODIUM_FOUND TRUE)
        set(SODIUM_INCLUDE_DIRS ${SODIUM_INCLUDE_DIR})
        set(SODIUM_LIBRARIES ${SODIUM_LIBRARY})
    endif()
endif()

if(NOT SODIUM_FOUND)
    message(FATAL_ERROR "
    
    ╔═══════════════════════════════════════════════════════════╗
    ║  libsodium НЕ НАЙДЕН!                                     ║
    ║                                                           ║
    ║  Открой терминал MSYS2 UCRT64 и выполни:                  ║
    ║  pacman -S mingw-w64-ucrt-x86_64-libsodium                ║
    ║                                                           ║
    ║  После установки перезапусти терминал и попробуй снова.   ║
    ╚═══════════════════════════════════════════════════════════╝
    ")
endif()

add_executable(gulib_client src/client/main.cpp)
add_executable(gulib_server src/server/main.cpp)

target_include_directories(gulib_client PRIVATE ${SODIUM_INCLUDE_DIRS})
target_include_directories(gulib_server PRIVATE ${SODIUM_INCLUDE_DIRS})

target_link_libraries(gulib_client PRIVATE ${SODIUM_LIBRARIES} ws2_32 wsock32)
target_link_libraries(gulib_server PRIVATE ${SODIUM_LIBRARIES} ws2_32 wsock32)

target_compile_options(gulib_client PRIVATE -O3 -march=native -flto)
target_compile_options(gulib_server PRIVATE -O3 -march=native -flto)
target_link_options(gulib_client PRIVATE -flto)
target_link_options(gulib_server PRIVATE -flto)

message(STATUS "gulib успешно настроен с libsodium!")
