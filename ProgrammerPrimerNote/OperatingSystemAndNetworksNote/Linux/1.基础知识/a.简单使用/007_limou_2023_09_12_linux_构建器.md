[TOC]

# 1.Make

## 1.1.Make 的概念

`make` 是一个指令，`makefile` 是一个在当前目录下存在的一个具有特定格式的文件。

如果在 `Linux` 中编写了大量的代码文件，难不成要一个一个使用 `gcc/h++` 编译吗，要是代码出了问题，修改后又需要编译怎么办？因此出现了 `make` 这样的自动化构建工具。 

## 1.2.Make 的使用

### 1.2.1.编写代码文件

先在一个目录下编写一个或多个 `.c` 源文件。

### 1.2.2.编写构建文件

1. 首先 `touch` 一个 `makefile` 文件

2. `makefile` 内部编写目标文件、依赖文件列表、依赖方法

3. 写入用作清理的伪目标，`.PHONY` 是修饰伪目标的关键字

```bash
# make 指令自顶向下执行，如果直接使用 make 指令就会执行第一份依赖命令

#目标文件: 依赖文件列表
#   依赖方法（Linux 下的命令）
#.PHONY: 伪目标（重点是执行方法而不是目的（“phony”即“假的，伪的”））
#   依赖方法

run:main.c #依赖关系
    gcc main.c -o run #依赖方法

.PHONY:clean #没有依赖关系，设定为伪目标
clean:
    rm -f run #依赖方法
```

4. 使用 `make` 指令运行 `makefile` 文件，不指定目标直接使用 `make` 指令，就会自动找第一对依赖关系和依赖方法（即 `makefile` 的第一个目标）。

### 1.2.3.运行构建工具

#### 1.2.3.1.尝试构建

```bash
# 尝试使用 make
$ ll
total 8
-rw-rw-r-- 1 limou limou 147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou  62 Sep 14 10:53 makefile
$ cat main.c
#include <stdio.h>
int main()
{
    int i = 0;
    for(i; i < 10; i++)
    {
        printf("%d ", i * i);
    }
    printf("\n");
    return 0;
}
$ cat makefile
run:main.c
    gcc main.c -o run
.PHONY:clean

clean:
    rm -f run

$ make run
gcc main.c -o run

$ ll
total 20
-rwxrwxr-x 1 limou limou 8408 Sep 14 10:54 run
-rw-rw-r-- 1 limou limou  147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou   62 Sep 14 10:53 makefile

$ ./run
0 1 4 9 16 25 36 49 64 81 

$ make clean
rm -f run

$ ll
total 8
-rw-rw-r-- 1 limou limou 147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou  62 Sep 14 10:53 makefile
```

#### 1.2.3.2.重复执行

很多书都会写：“伪目标总是会被执行的”。例如：

1. `make run/make` 指令是不能被重复调用的（会显示 `make:'run' is up to date.`。
   除非我们要编译的源文件的最近修改时间发生了变动，也就是说如果源文件没有经过修改，`make run` 是不会再次编译的，因为这样做的效率低下（大型的文件编译的时间非常久））。
   另外如果我们对源文件使用 `touch` 指令更新修改时间，那么就也是能重复使用 `make main` 指令。

2. 但 `make clean` 指令总是可以被调用的。因此“总是被执行”的另外一个说法是“总是会根据依赖关系，执行依赖方法”。

> 补充：使用 `stat` 可以查看文件的三个重要时间
> 
> 1. `Access`：文件最近被访问的时间（即：读取的时候，`touch` 只更改这个时间，但是由于 `Access` 也是属性，所以这个改动也会同时改动 `Change` 属性），如果修改了文件也算访问。不过由于读取文件是高频工作，因此 `Linux` 修改了内核，只会在一定时间后进行修改，不会读取一次就修改一次（每次都修改就会导致反复访问磁盘，导致效率低下）
> 
> 2. `Modify`：文件的最近内容修改时间（`make` 指令就是看这个文件来确认是否要重新编译的），不过有的时候改变内容会改变大小，导致文件的属性变化，`Change` 也发生了变化
> 
> 3. `Change`：文件的最近属性修改时间，比如：文件权限的设置、文件的大小改变

> 注意：文件改变的本质不是时间变化，而是随着时间文件内容产生新旧内容。

#### 1.2.3.3.自动推导

`make` 可以进行自动推导（嵌套代码），但是一般不建议像下面这么写，只是为了证明 `make` 可以自动推导这一现象：

```bash
run:main.o
    gcc main.o -o run
main.o:main.s
    gcc -c main.s -o main.o
mian.s:main.i
    gcc -S mian.i -o mian.s
mian.i:mian.c
    gcc -E main.c -o main.i

.PHONY:clean

clean:
    rm -f run main.i main.s main.o
```

#### 1.2.3.4.拓展学习

上面的知识在平时的学习和使用中就很够用了，但是我们可以拓展学习一些其他的内容。

1. 有的时候不希望打印出使用 `make` 命令的信息，这个时候可以在每个依赖方法前加上 `@` 符号，这样就不会回显信息了

2. 在 `makefile` 允许创建变量，因此我们上面的代码还可以改写为更加普遍的（变量的赋值一般不建议留空格，不过您其实可以将这种变量作为宏来理解）
   
   ```bash
   cc=gcc
   src=main.c
   target=run
   
   $(TAGET):$(src) #依赖关系
       $(cc) $(src) -o $(target) #依赖方法
   
   .PHONY:clean #伪目标
   clean:
       rm -f $(target) #依赖方法
   ```

3. `makefile` 还可以在依赖方法省略依赖关系用到的依赖文件列表和目标文件
   
   ```bash
   run:main.c #依赖关系
       gcc $^ -o $@ #依赖方法，$^表示冒号右侧内容，$@表示冒号左侧的内容
   
   .PHONY:clean #没有依赖关系，设定为伪目标
   clean:
       rm -f run #依赖方法
   ```


# 2.Cmake

## 2.1.Cmake 的概念

既然有了 `Make`，为什么会有 `CMake` 的存在呢？实际上，早期不同平台的 `Make` 面对大型项目难以维护，脚本编写和维护难度都比较高，并且不同平台有不同的编译链，迁移成本也比较高，因此就出现了 `CMake`，您可以 [前去 CMake 的官网查看 Camke 的历史](https://cmake.org/)，也可以 [去 Github 上查看 CMake 的源代码](https://github.com/Kitware/CMake)。另外，还有一份 [简单易懂的 CMake 官方教程](https://cmake-doc.readthedocs.io/zh-cn/latest/guide/tutorial/A%20Basic%20Starting%20Point.html) 也值得您查阅，不过也许是翻译的原因，有些地方读起来怪怪的，说得不是很清楚，所要这里关于 CMake 是对官方教程的补充的简化，旨在为您快速掌握 `CMake` 基础。

>   吐槽：在 `Cpp` 领域日渐被蚕食的今天，`CMake` 才是 `Cpp` 的未来。

## 2.2.Cmake 的使用

接下来我将在 `Linux` 系统上演示关于 `CMake` 的使用，我是用的是 `CMake 3.22.1`，如果您的版本何我不一样可能会有些一些操作需要变化，请您仔细查看本文。

### 2.2.1.CMake 的基本使用

和 `Make` 类似，`Cmake` 也需要编写构建文件 `CMakeList.txt`，但是编写过程更加简单，功能更加强大。简单点，现在假设您的项目结构如下所示。

```shell
# 项目结构
my_simple_project/
├── CMakeLists.txt
└── main.cc
```

在 `main.cpp` 文件中，您可以开始编写 `C/C++` 程序，例如：

```cpp
// main.cc: 计算给定数字的平方根并将结果输出到终端
#include <cmath>
#include <cstdlib>
#include <iostream>
#include <string>

int main(int argc, char* argv[]) {
    if (argc < 2) {
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
    }

    const double inputValue = atof(argv[1]);
    const double outputValue = sqrt(inputValue);
    
    std::cout << "The square root of " << inputValue << " is " << outputValue
        << std::endl;
    return 0;
}
```

然后开始 编写 `CMakeList.txt` 文件，`CMakeLists.txt` 文件主要由变量和指令构成，通过指令可以给一些 `CMake` 变量进行设置。

```cmake
# CMakeList.txt
cmake_minimum_required(VERSION 3.10) # 指定最小 CMake 版本

project(my_simple_project) # 项目的名字

set(CMAKE_CXX_STANDARD 11) # 指定 C++ 标准, 例如 C++11、C++14、C++17 等
set(CMAKE_CXX_STANDARD_REQUIRED True) # 确保编译器支持指定的 C++ 标准, 如果不支持就会停止配置功能

add_executable(my_executable main.cc) # 指定要构建的可执行文件和它的源代码文件
```

接下来要开始构建这个项目，不过您先需要在项目的根目录下（`MySimpleProject/`）==使用 `mkdir bulid && cd build`==，也就是创建构建文件夹后再进入该文件夹中开始 ==运行 `cmake ..` 命令== 来生成构建目标，这样对构建文件的处理会比较好一下，因为 `CMake` 会把构建的目标和其他的某些自带配置文件、缓存文件等也一起放入其中。

```sh
# 开始构建项目
$ ls
CMakeLists.txt  main.cc

$ mkdir build && cd build # 创建并进入构建目录
$ cmake .. # 运行 CMake 以生成构建系统
-- The C compiler identification is GNU 11.4.0
-- The CXX compiler identification is GNU 11.4.0
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
-- Configuring done
-- Generating done
-- Build files have been written to: /home/root/tests/cmake_test/build

$  tree
.
├── CMakeCache.txt
├── CMakeFiles
│   ├── 3.22.1
│   │   ├── CMakeCCompiler.cmake
│   │   ├── CMakeCXXCompiler.cmake
│   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   ├── CMakeSystem.cmake
│   │   ├── CompilerIdC
│   │   │   ├── a.out
│   │   │   ├── CMakeCCompilerId.c
│   │   │   └── tmp
│   │   └── CompilerIdCXX
│   │       ├── a.out
│   │       ├── CMakeCXXCompilerId.cpp
│   │       └── tmp
│   ├── cmake.check_cache
│   ├── CMakeDirectoryInformation.cmake
│   ├── CMakeOutput.log
│   ├── CMakeTmp
│   ├── Makefile2
│   ├── Makefile.cmake
│   ├── my_executable.dir
│   │   ├── build.make
│   │   ├── cmake_clean.cmake
│   │   ├── compiler_depend.make
│   │   ├── compiler_depend.ts
│   │   ├── DependInfo.cmake
│   │   ├── depend.make
│   │   ├── flags.make
│   │   ├── link.txt
│   │   └── progress.make
│   ├── progress.marks
│   └── TargetDirectories.txt
├── cmake_install.cmake
└── Makefile

8 directories, 28 files
```

不过 `CMake ..` 的执行并没有完成真正的编译构造，这一步只是执行了一系列的配置构建环境和生成构建文件，您可以看看上述执行后目录结构发生的变化，这里一步步解释多出来的文件。

-   `CMakeCache.txt` 缓存文件，保存了 `CMake` 配置过程中生成的变量和选项。它包括编译器路径、库路径、`CMake` 配置选项等。这个文件可以帮助 `CMake` 在后续配置中快速获取信息，而不需要重新执行所有的检测
-   `CMakeFiles/` 这是一个包含各种 `CMake` 构建信息和临时文件的目录
    -   `3.22.1/` 这是 `CMake` 版本目录，用于存放与特定 `CMake` 版本相关的文件
        -   `CMakeCCompiler.cmake` 记录了 `C` 编译器的配置，指导如何使用
        -   `CMakeCXXCompiler.cmake` 记录了 `C++` 编译器的配置，指导如何使用
        -   `CMakeDetermineCompilerABI_C.bin` 用于确定 `C` 编译器的 `ABI`（应用程序二进制接口）
        -   `CMakeDetermineCompilerABI_CXX.bin` 用于确定 `C++` 编译器的 `ABI`（应用程序二进制接口）
        -   `CMakeSystem.cmake` 记录系统信息，如操作系统、编译器版本等
        -   `CompilerIdC/` 内部的文件检测 `C` 编译器的工具和信息，验证编译器的有效性
        -   `CompilerIdCXX/` 内部的文件检测 `C++` 编译器的工具和信息，验证编译器的有效
        
    -   `cmake.check_cache` 缓存检查文件，在每次 `CMake` 运行时，它会检查 `CMakeCache.txt` 是否有变化。如果发现缓存中的某些设置或变量已被修改，有助于决定是否需要重新生成缓存或者进行重新配置

    -   `CMakeDirectoryInformation.cmake` 目录信息文件，记录构建目录的相关信息
    
    -   `CMakeOutput.log` 记录 `CMake` 配置过程的输出日志
    
    -   `CMakeTmp` 临时文件目录，用于存放在构建过程中生成的临时文件
    
    -   `Makefile2` 是一个由 `CMake` 生成的文件，通常作为 `Makefile` 的辅助文件，包含了构建过程的详细依赖规则和构建目标之间的依赖关系。它用于分离和组织 `Makefile` 中的复杂规则和依赖关系，使得主 `Makefile` 更加简洁
    
    -   `Makefile.cmake`：用于将 `CMake` 指令和变量转换为适合 `Makefile` 系统使用的格式，它帮助 `CMake` 将 `CMakeLists.txt` 中定义的配置和规则转换为 `Makefile` 可执行的具体指令
    
    -   `my_executable.dir/` 包含特定目标（例如 `my_executable`）的构建文件和信息，这里就不细细展开了，比较复杂...
    
    -   `progress.marks` 文件用于跟踪构建过程中的进度信息，特别是在使用 `make` 或其他构建工具进行并行构建时。这个文件帮助 `CMake` 了解哪些构建任务已经完成、哪些还在进行中，从而可以在命令行上显示构建进度
    
    -   `TargetDirectories.txt` 文件用于记录构建系统中各个目标（如可执行文件、库等）的输出目录信息。这个文件确保每个构建目标都能正确地生成到指定的目录中，并且这些目录的信息在整个构建过程中是可以访问的，避免路径错误或文件错位的情况
    
-   `cmake_install.cmake` 安装脚本文件，生成用于安装构建产物的 `CMake` 脚本，它通常用于执行 `make install` 或 `cmake --install` 命令时，指定如何安装编译生成的文件和库
-   `Makefile` 生成的 `Makefile` 文件，用于 `make` 命令。它包含了构建项目所需的所有构建规则和依赖关系，`make` 命令会根据这个文件执行编译、链接和其他构建任务

`OK`，现在您已经简单了解了 `CMake` 的构建系统，现在就可以开始编译了，使用 `cmake --build .` 替代 `make` 编译程序（比 `make` 会更加具有可移植性）。

```shell
# 开始编译源代码
$ cmake --build .
[ 50%] Building CXX object CMakeFiles/my_executable.dir/main.cc.o
[100%] Linking CXX executable my_executable
[100%] Built target my_executable

$ ls
CMakeFiles  CMakeCache.txt  cmake_install.cmake  Makefile  my_executable\

$ ./my_executable
Usage: ./my_executable number

$ ./my_executable 8
The square root of 8 is 2.82843
```

这将生成一个可执行文件 `my_executable`（或在 `Windows` 上可能是 `my_executable.exe`），您可以运行它来执行程序。

### 2.2.2.CMake 的常用指令

虽然每个小节只讲一个指令，但是有可能不得不混入一些其他没有和您说明的指令，这些指令我会给出注释...

#### 2.2.2.1.message()

```shell
# message(): 打印日志信息
message([<mode>] "messag...")
# 使用这个指令可以用来输出一些构建信息, <mode> 是消息的等级
# STATUS 显示普通的消息, 通常用于显示信息
# WARNING 显示警告消息, 通常用于提示潜在的问题
# SEND_ERROR 显示错误消息并设置 CMake 生成失败, 后续的生成过程会中止
# FATAL_ERROR 显示致命错误消息并立即停止生成过程
# DEPRECATION 显示弃用警告消息
```

我们后面会频繁使用这个 `message()` 开打印信息，所以这里就不展示使用了。

#### 2.2.2.2.project()

```cmake
# project(): 用于配置项目名称和版本号
project(项目名称 VERSION 主版本号.次版本号.修补版本号.微小补修版本号)
# 当 CMake 执行 project() 后会设置以下变量, 可以使用 ${变量名} 引用变量的值
# PROJECT_NAME 项目名称
# PROJECT_VERSION 项目的完整版本号
# PROJECT_VERSION_MAJOR 项目的主版本号
# PROJECT_VERSION_MINOR 项目的次版本号
# PROJECT_VERSION_PATCH 项目的修补版本号(若无指定就为空)
# PROJECT_VERSION_TWEAK 项目的微小修补版本号(若无指定就为空)
```

```shell
# 使用 project()
$ s
CMakeLists.txt  main.cc

$ cat CMakeLists.txt
# CMakeList.txt
cmake_minimum_required(VERSION 3.10) # 指定最小 CMake 版本

project(my_simple_project VERSION 1.2.3.4) # 项目的名字

# 打印项目信息
message(STATUS "PROJECT_NAME: ${PROJECT_NAME}")
message(STATUS "PROJECT_VERSION: ${PROJECT_VERSION}")
message(STATUS "PROJECT_VERSION_MAJOR: ${PROJECT_VERSION_MAJOR}")
message(STATUS "PROJECT_VERSION_MINOR: ${PROJECT_VERSION_MINOR}")
message(STATUS "PROJECT_VERSION_PATCH: ${PROJECT_VERSION_PATCH}")
message(STATUS "PROJECT_VERSION_TWEAK: ${PROJECT_VERSION_TWEAK}")

set(CMAKE_CXX_STANDARD 11) # 指定 C++ 标准, 例如 C++11、C++14、C++17 等
set(CMAKE_CXX_STANDARD_REQUIRED True) # 确保编译器支持指定的 C++ 标准, 如果不支持就会停止配置功能

add_executable(my_executable main.cc) # 指定要构建的可执行文件和它的源代码文件

$ mkdir build && cd build && cmake .. && cmake --build .
-- The C compiler identification is GNU 11.4.0
-- The CXX compiler identification is GNU 11.4.0
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
-- PROJECT_NAME: my_simple_project
-- PROJECT_VERSION: 1.2.3.4
-- PROJECT_VERSION_MAJOR: 1
-- PROJECT_VERSION_MINOR: 2
-- PROJECT_VERSION_PATCH: 3
-- PROJECT_VERSION_TWEAK: 4
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ljp/tests/cmake_test/build
[ 50%] Building CXX object CMakeFiles/my_executable.dir/main.cc.o
[100%] Linking CXX executable my_executable
[100%] Built target my_executable
```

#### 2.2.2.2.configure_file()

```cmake
# configure_file(): 用于处理配置文件模板 .in 并生成实际的配置文件 .h
configure_file(<input> <output> [COPYONLY] [ESCAPE_QUOTES] [@ONLY])
# configure_file() 
# <input> 输入模板文件的路径(xxx.h.in)
# <output> 输出文件的路径(xxx.h), 默认输出到 cmake --bulid . 时的 . 目录下
# COPYONLY 仅将文件从源目录复制到目标目录, 而不进行变量替换
# ESCAPE_QUOTES 转义输出文件中的引号字符(用于处理包含引号的内容)
# @ONLY 仅替换以 @VAR@ 形式出现的变量, 不替换 ${VAR} 形式的变量, 不过这种场景我暂时没有找到, 待补充...
```

```shell
# 使用 configure_file()
$ ls
CMakeLists.txt  main.cc  main.h.in

$ cat *
# CMakeList.txt
cmake_minimum_required(VERSION 3.10) # 指定最小 CMake 版本

project(my_simple_project VERSION 1.2.3.4) # 项目的名字

configure_file(main.h.in main.h) # 配置文件的处理, 由于生成的头文件默认输出到 cmake --bulid . 时的 . 目录下, 也就是 ${CMAKE_BINARY_DIR} 的值, 因此后面需要把源文件的头文件目录 target_include_directories() 指定一下, 否则找不到头文件

set(CMAKE_CXX_STANDARD 11) # 指定 C++ 标准, 例如 C++11、C++14、C++17 等
set(CMAKE_CXX_STANDARD_REQUIRED True) # 确保编译器支持指定的 C++ 标准, 如果不支持就会停止配置功能

add_executable(my_executable main.cc) # 指定要构建的可执行文件和它的源代码文件

target_include_directories(my_executable PRIVATE ${CMAKE_BINARY_DIR}) # 将构建目录添加到包含目录列表中, 这样编译时才可以找到对应的头文件. 而这里的 PRIVATE 意味着只有在编译当前目标时, 编译器会查找这些目录中的头文件, 这里 ${CMAKE_BINARY_DIR} 值等价于 /home/root/tests/cmake_test/bulid

// main.cc: 打印头文件中的宏
#include <iostream>
#include "main.h"

int main() {
    std::cout << "Tutorial Version: "
              << MAIN_VERSION_MAJOR << "."
              << MAIN_VERSION_MINOR << "."
              << MAIN_VERSION_PATCH << "."
              << MAIN_VERSION_TWEAK << std::endl;

    return 0;
}

// main.h.in: 模板文件
#ifndef MAIN_H
#define MAIN_H

#define MAIN_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#define MAIN_VERSION_MINOR @PROJECT_VERSION_MINOR@
#define MAIN_VERSION_PATCH @PROJECT_VERSION_PATCH@
#define MAIN_VERSION_TWEAK @PROJECT_VERSION_TWEAK@

#endif // MAIN_H

$ mkdir build && cd build && cmake .. && cmake --build . && ./my_executable  && cd ..
-- The C compiler identification is GNU 11.4.0
-- The CXX compiler identification is GNU 11.4.0
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
-- PROJECT_NAME: my_simple_project
-- PROJECT_VERSION: 1.2.3.4
-- PROJECT_VERSION_MAJOR: 1
-- PROJECT_VERSION_MINOR: 2
-- PROJECT_VERSION_PATCH: 3
-- PROJECT_VERSION_TWEAK: 4
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ljp/tests/cmake_test/build
[ 50%] Building CXX object CMakeFiles/my_executable.dir/main.cc.o
[100%] Linking CXX executable my_executable
[100%] Built target my_executable
Tutorial Version: 1.2.3.4
```



这里我制作一个模板文件 `.h.in` 做个例子。

>   补充：`${CMAKE_BINARY_DIR}` 是由 `CMake` 在配置阶段自动设置的变量，它的值是构建目录的路径，即您在运行 `CMake` 时指定的目录。
>
>   如果你在项目根目录中创建了一个 `build` 子目录，并在其中运行 CMake 配置命令，如：
>
>   ```shell
>   mkdir build && cd build && cmake ..
>   ```
>
>   此时 `${CMAKE_BINARY_DIR}` 将被设置为 `build` 目录的路径，例如 `/path/to/project/build`，在使用一些指令时会自动添加这个路径信息。

>   补充：上述之所以把生成的 `.h` 配置文件放在了 `bulid` 的内部，主要原因是因为可以让所有是 `CMake` 产生的文件都处于 `bulid` 中方便管理。

>   吐槽：不知道为什么，翻译版的官方文档把构建目录 `build` 称为二进制目录，搞得我疑惑了好久...

#### 2.2.2.3.set()

```cmake
# set(): 创建或设置 CMake 变量
set(变量名, 设定值)
# 使用 set() 可以创建或设置 CMake 变量或用户自己设置的自定义变量, 不过用户在创建自定义变量的时候, 命名应该尽量避免使用 CMAKE_ 打头, 例如 CMAKE_BINARY_DIR, 这些变量有些 CMake 已经帮我们在 CMake 一运行的时候就设置好了, 再修改有可能引发奇怪的错误。也有的可以交给用户变量来设置，例如 CMAKE_CXX_STANDARD, CMAKE_CXX_STANDARD_REQUIRED
```

我们上面也已经用过，这里只是简单补全一下概念。

#### 2.2.2.4.add_library()



### 2.2.3.高级使用 CMake

如果可以，我们在掌握了以上的所有知识后，应该给与一个实际的项目使用 `CMake` 进行管理，下面我给出一个比较接近实际工程的 `Cpp` 项目目录，并且编写对应的 `CMakeLists.txt` 文件。

```shell
# Cpp 工程目录
$ tree
project_name/
├── .git # git 存储仓库
├── .gitignore # git 忽略文件
├── 3rdparty/ # 第三方库目录
│   ├── gtest/ # Goolge 单元测试框架
│   │   ├── include/
│   │   └── lib/
│   ├── 3rdparty_1/
│   │   ├── include/
│   │   └── lib/
│   └── 3rdparty_2/
│       ├── include/
│       └── lib/
├── project_name/ # 项目源代码目录
│   ├── main.h # 主程序头文件
│   ├── main.cc # 主程序源文件
│   ├── module1/ # 项目主模块1
│   │   ├── dir_1/ # 子模块1
│   │   ├── dir_2/ # 子模块2
│   │   ├── module1.cc
│   │   └── module1.h
│   └── module2/ # 项目主模块2
│       ├── module2.cc
│       └── module2.h
├── tests/ # 项目测试代码目录
│   ├── test_main.cc # 主程序单元测试
│   ├── test_module1.cc # 主模块1单元测试
│   └── test_module2.cc # 主模块2单元测试
├── CMakeLists.txt # 使用 CMake 管理项目构建
├── build/ # 存放 CMake 后的构建系统文件和目标文件
├── LICENSE # 项目许可证信息
├── README.md # 项目说明文档
├── doc/doxygen.html # 项目指导文档
└── deploy/ # 存放可以对外交付/部署的文件
    ├── bin/ # 可交付可执行的二进制文件(用户可以直接使用)
    ├── include/ # 可交付头文件(用户可以调用接口)
    └── lib/ # 可交付库文件(支持用户调用接口对应的实现)
```

则我们需要对应的 `CMakeLists.txt` 内容如下举例。

```cmake
# CMakeList.txt
# 指定 CMake 最低版本
cmake_minimum_required(VERSION 3.10)

# 设置项目名称和版本号
project(project_name VERSION 1.0 LANGUAGES CXX)

# 强制指定 C++ 标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 设置变量
set(SOURCE_DIR "${CMAKE_SOURCE_DIR}/project_name")
set(INCLUDE_DIR "${CMAKE_SOURCE_DIR}/project_name")
set(THIRD_PARTY_DIR "${CMAKE_SOURCE_DIR}/3rdparty")
set(TEST_DIR "${CMAKE_SOURCE_DIR}/tests")

# 添加全局项目头文件目录
include_directories(
    ${INCLUDE_DIR}
    ${INCLUDE_DIR}/module1
    ${INCLUDE_DIR}/module2
    ${THIRD_PARTY_DIR}/3rdparty_1/include
    ${THIRD_PARTY_DIR}/3rdparty_2/include
)

# 创建项目可执行文件
set(SOURCES
    ${SOURCE_DIR}/main.cc
    ${SOURCE_DIR}/module1/module1.cc
    ${SOURCE_DIR}/module2/module2.cc
)
add_executable(${PROJECT_NAME}_executable ${SOURCES})

# 链接第三方库
target_link_libraries(${PROJECT_NAME}_executable
    ${THIRD_PARTY_DIR}/3rdparty_1/lib/libname1.a
    ${THIRD_PARTY_DIR}/3rdparty_2/lib/libname2.a
)

# 打开单元测试
enable_testing()

# 创建测试可执行文件
add_executable(test_module1 ${TEST_DIR}/test_module1.cc)
add_executable(test_module2 ${TEST_DIR}/test_module2.cc)
add_executable(test_main ${TEST_DIR}/test_main.cc)

# 链接 Google Test 库到测试目标
target_link_libraries(test_module1
    ${PROJECT_NAME}_executable
    ${THIRD_PARTY_DIR}/gtest/lib/libgtest.a
)

target_link_libraries(test_module2
    ${PROJECT_NAME}_executable
    ${THIRD_PARTY_DIR}/gtest/lib/libgtest.a
)

target_link_libraries(test_main
    ${PROJECT_NAME}_executable
    ${THIRD_PARTY_DIR}/gtest/lib/libgtest.a
)

# 添加测试
add_test(NAME TestModule1 COMMAND test_module1)
add_test(NAME TestModule2 COMMAND test_module2)
add_test(NAME TestMain COMMAND test_main)

# 设置安装规则
install(TARGETS ${PROJECT_NAME}_executable DESTINATION deploy/bin)
install(DIRECTORY ${INCLUDE_DIR}/ DESTINATION deploy/include)
install(DIRECTORY ${THIRD_PARTY_DIR}/3rdparty_1/include/ DESTINATION deploy/include)
install(DIRECTORY ${THIRD_PARTY_DIR}/3rdparty_2/include/ DESTINATION deploy/include)
install(DIRECTORY ${CMAKE_BINARY_DIR}/lib/ DESTINATION deploy/lib)
```





