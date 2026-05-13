[![CI](https://github.com/shirmanovak410-ops/lab07/actions/workflows/ci.yml/badge.svg)](https://github.com/shirmanovak410-ops/lab07/actions/workflows/ci.yml)
# Lab07
В рамках выполнения данной лабораторной работы мною были выполнены команды из tutorial с заменой устаревшего hunter на FetchContent:
1) Скопирован репозиторий из lab06.
2) В соответствие с tutorial были установлен hunter и изменён CMakeLists.txt, но в процессе сборки произошла ошибка, связанная с версией компилятора
```bash
$ git clone https://github.com/shirmanovak410-ops/lab06 projects/lab07
$ cd projects/lab07
$ git remote remove origin
$ git remote add origin https://github.com/shirmanovak410-ops/lab07
3) В соответствие с tutorial были установлен hunter и изменён CMakeLists.txt, но в процессе сборки произошла ошибка, связанная с версией компилятора
Мною было принято решение подключить более современный FetchContent
```
3) Для этого в CMakeLists.txt были внесены следующие изменения:
```bash
include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        v1.15.2
)
FetchContent_MakeAvailable(googletest)

if(BUILD_TESTS)
    enable_testing()
    target_link_libraries(check ${PROJECT_NAME} GTest::gtest_main)
endif()
```
4) Была произведена ручная сборка и запущен тест
```bash
$ cmake -H. -B_builds -DBUILD_TESTS=ON
-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (7.5s)
-- Generating done (0.1s)
-- Build files have been written to: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds
$ cmake --build _builds
[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
$ cmake --build _builds --target test
Running tests...
Test project /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```
FetchContent успешно скачал и собрал GTest
```bash
$ ls -la _builds/_deps/
ls -la _install/
итого 12
drwxrwxr-x  3 ksu ksu 4096 мая 13 13:56 .
drwxrwxr-x 14 ksu ksu 4096 мая 13 13:56 ..
drwxrwxr-x  6 ksu ksu 4096 мая 13 13:56 default
```
5) Написан demo и добавлен в CMakeLists.txt
```cpp
#include <iostream>
#include <fstream>
#include <cstdlib>
#include <string>

int main(int argc, char* argv[])
{
    const char* log_path = std::getenv("LOG_PATH");
    if (log_path == nullptr)
    {
        std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
        return 1;
    }

    std::string text;
    while (std::cin >> text)
    {
        std::ofstream out{log_path, std::ios_base::app};
        print(text, out);
        out << std::endl;
    }
    return 0;
}
```

6) Подключён модуль polly и установлен clang

7) Через polly был запущен тест
```bash
$ tools/polly/bin/polly.py --test
Python version: 3.13
Build dir: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "--version"

cmake version 3.31.6

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/tools/polly/default.cmake`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "-H." "-B/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/tools/polly/default.cmake"

-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (8.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default`
  `--`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "--build" "/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default" "--"

[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
Run tests
Execute command: [
  `ctest`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default]> "ctest"

Test project /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/default
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
-
Log saved: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:09.354556s
Build: 0:00:24.114430s
Test: 0:00:00.035098s
-
Total: 0:00:33.504265s
-
SUCCESS
```
8) Произведена сборка и установка в _install
```bash
$ tr
ee _install/
_install/
└── default
    ├── bin
    │   └── demo
    ├── cmake
    │   ├── print-config.cmake
    │   └── print-config-noconfig.cmake
    ├── include
    │   ├── gmock
    │   │   ├── gmock-actions.h
    │   │   ├── gmock-cardinalities.h
    │   │   ├── gmock-function-mocker.h
    │   │   ├── gmock.h
    │   │   ├── gmock-matchers.h
    │   │   ├── gmock-more-actions.h
    │   │   ├── gmock-more-matchers.h
    │   │   ├── gmock-nice-strict.h
    │   │   ├── gmock-spec-builders.h
    │   │   └── internal
    │   │       ├── custom
    │   │       │   ├── gmock-generated-actions.h
    │   │       │   ├── gmock-matchers.h
    │   │       │   ├── gmock-port.h
    │   │       │   └── README.md
    │   │       ├── gmock-internal-utils.h
    │   │       ├── gmock-port.h
    │   │       └── gmock-pp.h
    │   ├── gtest
    │   │   ├── gtest-assertion-result.h
    │   │   ├── gtest-death-test.h
    │   │   ├── gtest.h
    │   │   ├── gtest-matchers.h
    │   │   ├── gtest-message.h
    │   │   ├── gtest-param-test.h
    │   │   ├── gtest_pred_impl.h
    │   │   ├── gtest-printers.h
    │   │   ├── gtest_prod.h
    │   │   ├── gtest-spi.h
    │   │   ├── gtest-test-part.h
    │   │   ├── gtest-typed-test.h
    │   │   └── internal
    │   │       ├── custom
    │   │       │   ├── gtest.h
    │   │       │   ├── gtest-port.h
    │   │       │   ├── gtest-printers.h
    │   │       │   └── README.md
    │   │       ├── gtest-death-test-internal.h
    │   │       ├── gtest-filepath.h
    │   │       ├── gtest-internal.h
    │   │       ├── gtest-param-util.h
    │   │       ├── gtest-port-arch.h
    │   │       ├── gtest-port.h
    │   │       ├── gtest-string.h
    │   │       └── gtest-type-util.h
    │   └── print.hpp
    └── lib
        ├── cmake
        │   └── GTest
        │       ├── GTestConfig.cmake
        │       ├── GTestConfigVersion.cmake
        │       ├── GTestTargets.cmake
        │       └── GTestTargets-noconfig.cmake
        ├── libgmock.a
        ├── libgmock_main.a
        ├── libgtest.a
        ├── libgtest_main.a
        ├── libprint.a
        └── pkgconfig
            ├── gmock_main.pc
            ├── gmock.pc
            ├── gtest_main.pc
            └── gtest.pc

15 directories, 57 files
```
9) Произведена успешная сборка на другом компиляторе (clang)
```bash
$ tools/polly/bin/polly.py --toolchain clang-cxx14
Python version: 3.13
Build dir: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14
Execute command: [
  `which`
  `cmake`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "--version"

cmake version 3.31.6

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14`
  `-GUnix Makefiles`
  `-DCMAKE_TOOLCHAIN_FILE=/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/tools/polly/clang-cxx14.cmake`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "-H." "-B/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14" "-GUnix Makefiles" "-DCMAKE_TOOLCHAIN_FILE=/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/tools/polly/clang-cxx14.cmake"

-- [polly] Used toolchain: clang / c++14 support
-- The C compiler identification is Clang 19.1.7
-- The CXX compiler identification is Clang 19.1.7
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/clang - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/clang++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (11.6s)
-- Generating done (0.0s)
-- Build files have been written to: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14
Execute command: [
  `cmake`
  `--build`
  `/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14`
  `--`
]

[/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07]> "cmake" "--build" "/home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_builds/clang-cxx14" "--"

[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
warning: unknown warning option '-Wno-maybe-uninitialized'; did you mean '-Wno-uninitialized'? [-Wunknown-warning-option]
1 warning generated.
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
-
Log saved: /home/ksu/shirmanovak410-ops/workspace/projects/lab06/projects/lab07/_logs/polly/clang-cxx14/log.txt
-
Generate: 0:00:13.596409s
Build: 0:00:25.465170s
-
Total: 0:00:39.061803s
-
SUCCESS
```
