cmake_minimum_required(VERSION 3.4)
 
project(solver)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(solver solver_application/equation.cpp)

add_library(solver_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/solver_lib/solver.cpp)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib ${CMAKE_CURRENT_BINARY_DIR}/formatter_ex_lib)

target_include_directories( solver PUBLIC # позволяем видеть директории
${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/
${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/
${CMAKE_CURRENT_SOURCE_DIR}/solver_lib)

target_link_libraries(solver formatter_ex_lib solver_lib) # связываем (подключаем) библиотеки к solver

install(TARGETS solver # задаем инструкции по установки
	RUNTIME DESTINATION bin
)

include(CPackConfig.cmake)
