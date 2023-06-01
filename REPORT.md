# Лабораторная работа №6

### Цель - изучение средств пакетирования на примере CPack.

Из lab3 скопирую следующие дирректории:
```
formatter_ex_lib
formatter_lib
hello_world_application
solver_application
solver_lib 
```

Cоздам CMakeLists.txt:
```
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
```

Создам DESCRIPTION, LICENSE, README.md<br/>

Создам CPackConfig.cmake:
```
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_CONTACT a0730c@gmail.com)
set(CPACK_PACKAGE_VERSION ${PRINT_VERSION})
set(CPACK_PACKAGE_NAME "solverapp")
set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "solver application")
set(CPACK_PACKAGE_VENDOR "a0730c")
set(CPACK_PACKAGE_PACK_NAME "solver-${PRINT_VERSION}")

set(CPACK_SOURCE_INSTALLED_DIRECTORIES 
   "${CMAKE_SOURCE_DIR}/solver_application; solver_application"
   "${CMAKE_SOURCE_DIR}/solver_lib; solver_lib"
   "${CMAKE_SOURCE_DIR}/formatter_ex_lib; formatter_ex_lib"
   "${CMAKE_SOURCE_DIR}/formatter_lib; formatter_lib")

set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")
set(CPACK_GENERATOR "DEB;RPM")

set(CPACK_DEBIAN_PACKAGE_VERSION ${PRINT_VERSION})
set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "all")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Solves quadratic equations")

set(CPACK_RPM_PACKAGE_SUMMARY "Solves quadratic equations")

include(CPack)
```

Создам workflow:
```
name: CMake

on:
 push:
   tags:
     - v**

jobs: 

  build_packages_Linux:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Configure Solver
      run: cmake ${{github.workspace}} -B ${{github.workspace}}/build -D PRINT_VERSION=${GITHUB_REF_NAME#v}

    - name: Build Solver
      run: cmake --build ${{github.workspace}}/build

    - name: Build package
      run: cmake --build ${{github.workspace}}/build --target package

    - name: Build source package
      run: cmake --build ${{github.workspace}}/build --target package_source

    - name: Make a release
      uses: ncipollo/release-action@v1.10.0
      with:
        artifacts: "build/*.deb,build/*.rpm,build/*.tar.gz,build/*.zip"
        token: ${{ secrets.GITHUB_TOKEN }}
```

Выдает ошибку в actions: **Error 403: Resource not accessible by integration.**<br/>
Нашла решение в интернете:<br/>
В настройках репозитория **actions, Workflow permissions** поставить опцию **Read and write**<br/>

Создам tag и запушу:
```
$ git tag v0.1
$ git push origin v0.1
```

 
