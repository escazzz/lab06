cmake_minimum_required(VERSION 3.2)

project(formatter_ex)

add_library(formatter_lib STATIC ../formatter_lib/formatter.cpp)

target_include_directories(formatter_lib PUBLIC ../formatter_lib)

add_library(formatter_ex_lib STATIC formatter_ex.h formatter_ex.cpp)

target_link_libraries(formatter_ex_lib formatter_lib)
