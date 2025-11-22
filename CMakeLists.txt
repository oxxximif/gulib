cmake_minimum_required(VERSION 3.24)
project(gulib LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Предупреждения и оптимизации
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    add_compile_options(-Wall -Wextra -Wpedantic)
elseif(MSVC)
    add_compile_options(/W4 /permissive- /utf-8)
endif()

# Клиент
add_executable(gulib_client
    src/client/main.cpp
)

# Сервер
add_executable(gulib_server
    src/server/main.cpp
)

# Winsock2 для Windows
if(WIN32)
    target_link_libraries(gulib_client PRIVATE ws2_32 wsock32)
    target_link_libraries(gulib_server PRIVATE ws2_32 wsock32)
endif()

message(STATUS "gulib готов к сборке! Используй: cmake -S . -B build && cmake --build build")
