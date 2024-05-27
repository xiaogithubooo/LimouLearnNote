[TOC]

# 1.make

## 1.1.make 的概念

`make` 是一个指令，`makefile` 是一个在当前目录下存在的一个具有特定格式的文件。

如果在 `Linux` 中编写了大量的代码文件，难不成要一个一个使用 `gcc/h++` 编译吗，要是代码出了问题，修改后又需要编译怎么办？因此出现了 `make` 这样的自动化构建工具。 

## 1.2.make 的使用

### 1.2.1.编写代码文件

先在一个目录下编写一个或多个 `.c` 源文件。

### 1.2.2.编写构建文件

1. 首先 `touch` 一个 `makefile` 文件

2. `makefile` 内部编写目标文件、依赖文件列表、依赖方法

3. 写入用作清理的伪目标，`.PHONY` 是修饰伪目标的关键字

```bash
ake 指令自顶向下执行，如果直接使用 make 指令就会执行第一份依赖命令

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


# 2.cmake

## 2.1.cmake 的概念



## 2.2.cmake 的使用

和 `make` 类似，`cmake` 也需要编写构建文件 `CMakeList.txt`，但是编写过程更加复杂，功能更加强大。

假设您的项目结构如下所示：

```shell
# 项目结构
MySimpleProject/
│
├── CMakeLists.txt # 项目根目录下的 CMakeLists.txt 文件
└── src/
    └── main.cpp # 包含程序入口点的源代码文件
```

在 `main.cpp` 文件中，您可以开始编写 `C/C++` 程序，例如：

```cpp
// src/main.cpp
#include <iostream>
int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

使用三个基本命令，是一个基本的 `CMakeLists.txt` 文件示例，它演示了如何从单个源代码文件构建一个简单的可执行文件：

```cmake
# CMakeList.txt
# 指定最小 CMake 版本
cmake_minimum_required(VERSION 3.10)

# 启动一个项目
project(MySimpleProject)

# 指定 C++ 标准, 例如 C++11、C++14、C++17 等
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED YES) # 确保编译器支持指定的 C++ 标准, 如果不支持就会停止配置功能

# 指定要构建的可执行文件和它的源代码文件
add_executable(MyExecutable main.cpp)
```

在这个示例中：

1. `cmake_minimum_required(VERSION 3.10)` 命令指定了构建此项目所需的最小 `CMake` 版本。

2. `project(MySimpleProject)` 命令初始化了项目，`MySimpleProject` 是项目的名称，这个命令设置了项目的元数据，如名称和版本。

3. `add_executable(MyExecutable main.cpp)` 命令告诉 `CMake` 要构建一个可执行文件，名称为 `MyExecutable`，它由 `main.cpp` 源代码文件编译而成。

要构建这个项目，你需要在项目根目录下（`MySimpleProject/`）运行 CMake 命令来生成构建系统，然后编译项目：

```sh
# 开始构建项目
cd MySimpleProject
mkdir build && cd build # 创建并进入构建目录
cmake .. # 运行 CMake 以生成构建系统
cmake --build . # 编译项目
```

这将生成一个可执行文件 `MyExecutable`（或在 `Windows` 上可能是 `MyExecutable.exe`），您可以运行它来执行程序。

