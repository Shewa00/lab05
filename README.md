[![Build Status](https://travis-ci.org/Shewa00/lab04.svg?branch=master)](https://travis-ci.org/Shewa00/lab04)
## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [X] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [X] 2. Ознакомиться со ссылками учебного материала
- [X] 3. Выполнить инструкцию учебного материала
- [X] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>
```

# Заходим в свой workspace и активируем скрипты
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

#Работа с гит
```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03

Cloning into 'projects/lab03'...
remote: Enumerating objects: 4292, done.
remote: Total 4292 (delta 0), reused 0 (delta 0), pack-reused 4292
Receiving objects: 100% (4292/4292), 16.96 MiB | 114.00 KiB/s, done.
Resolving deltas: 100% (713/713), done.

$ cd projects/lab03
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```

#Работаем с файлами
```ShellSession
$ g++ -std=c++11 -I./include -c sources/print.cpp
$ ls print.o
print.o

$ nm print.o | grep print
0000000000000000 T __Z5printRKNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEERNS_13basic_ostreamIcS2_EE
0000000000000080 T __Z5printRKNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEERNS_14basic_ofstreamIcS2_EE

$ ar rvs print.a print.o
ar: creating archive print.a
a - print.o

$ file print.a
print.a: current ar archive random library

$ g++ -std=c++11 -I./include -c examples/example1.cpp
$ ls example1.o
example1.o

$ g++ example1.o print.a -o example1
$ ./example1 && echo
hello

```

```ShellSession
$ g++ -std=c++11 -I./include -c examples/example2.cpp
$ nm example2.o
000000000000334c s GCC_except_table0
0000000000003364 s GCC_except_table1
000000000000339c s GCC_except_table10
00000000000033ac s GCC_except_table24
00000000000033bc s GCC_except_table35
00000000000033cc s GCC_except_table83
00000000000033ec s GCC_except_table84
0000000000003384 s GCC_except_table9
                 U __Unwind_Resume
                 U __Z5printRKNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEERNS_14basic_ofstreamIcS2_EE
...
0000000000000000 T _main
                 U _memcpy
                 U _memmove
                 U _memset
                 U _strlen
                 

$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
hello

```

#Удаление файлов
```ShellSession
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

#Создание конфигурации
```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

#Устанавливаем 11 стандарт
```ShellSession
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

#Добавляем библиотеку
```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

#Указываем дирректорию
```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

#Сборка проекта
```ShellSession
$ cmake -H. -B_build
-- The C compiler identification is AppleClang 10.0.1.10010046
-- The CXX compiler identification is AppleClang 10.0.1.10010046
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/macbook/Shewa00/workspace/projects/lab03/_build

$ cmake --build _build
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print

```

#Подаем аргументы
```ShellSession
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

#Добавляем target_link
```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

#Сборка проекта
```ShellSession
$ cmake --build _build
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/macbook/Shewa00/workspace/projects/lab03/_build
[ 33%] Built target print
Scanning dependencies of target example2
[ 50%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[ 66%] Linking CXX executable example2
[ 66%] Built target example2
Scanning dependencies of target example1
[ 83%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[100%] Linking CXX executable example1
[100%] Built target example1

$ cmake --build _build --target print
[100%] Built target print

$ cmake --build _build --target example1
[ 50%] Built target print
[100%] Built target example1

$ cmake --build _build --target example2
[ 50%] Built target print
[100%] Built target example2

```

#Работаем с проектом
```ShellSession
$ ls -la _build/libprint.a
-rw-r--r--  1 macbook  staff  13456 13 апр 22:50 _build/libprint.a

$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

#Работаем с репозиторием
```ShellSession
$ git clone https://github.com/tp-labs/lab03 tmp
Cloning into 'tmp'...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 70 (delta 6), reused 22 (delta 4), pack-reused 43
Unpacking objects: 100% (70/70), done.

$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```ShellSession
$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/macbook/Shewa00/workspace/projects/lab03/_build

$ cmake --build _build --target install
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /Users/macbook/Shewa00/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /Users/macbook/Shewa00/workspace/projects/lab03/_install/include
-- Installing: /Users/macbook/Shewa00/workspace/projects/lab03/_install/include/print.hpp
-- Installing: /Users/macbook/Shewa00/workspace/projects/lab03/_install/cmake/print-config.cmake
-- Installing: /Users/macbook/Shewa00/workspace/projects/lab03/_install/cmake/print-config-noconfig.cmake


$ tree _install
_install
├── cmake
│   ├── print-config-noconfig.cmake
│   └── print-config.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a

3 directories, 4 files

```

```ShellSession
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
[master 8e89eba] added CMakeLists.txt
 1 file changed, 36 insertions(+)
 create mode 100644 CMakeLists.txt

$ git push origin master
remote: Enumerating objects: 70, done.
remote: Counting objects: 100% (70/70), done.
remote: Compressing objects: 100% (42/42), done.
remote: Total 70 (delta 30), reused 62 (delta 25), pack-reused 0
Unpacking objects: 100% (70/70), done.
From https://github.com/Shewa00/lab03
 * [new branch]      master     -> origin/master

```

## Report

```ShellSession
$ popd
~/Shewa00/workspace

$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
Cloning into 'tasks/lab03'...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 70 (delta 6), reused 22 (delta 4), pack-reused 43
Unpacking objects: 100% (70/70), done.

$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
