cmake_minimum_required(VERSION 3.2)

project(Solver)

add_library(formatter_lib STATIC ../formatter_lib/formatter.cpp)

target_include_directories(formatter_lib PUBLIC ../formatter_lib)

add_library(solver STATIC ../solver_lib/solver.cpp)

target_include_directories(solver PUBLIC ../solver_lib)

add_library(formatter_ex_lib STATIC ../formatter_ex_lib/formatter_ex.cpp)

target_include_directories(formatter_ex_lib PUBLIC ../formatter_ex_lib)

add_executable(solver_app equation.cpp)

target_link_libraries(formatter_ex_lib formatter_lib)

target_link_libraries(solver_app formatter_ex_lib)

target_link_libraries(solver_app solver)
