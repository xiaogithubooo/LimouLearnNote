# 1.debug/release

`gdb`是一款`Linux`下的一款调试器，在没有图形化界面下，是一种不错的调试方案（虽然在一般的开发环境中很少会使用`gdb`）

不过要使用`gdb`，就先要了解`debug`和`release`版本。

发布软件的时候有一种叫`debug`版本，另外一种叫`release`版本，在`debug`中包含了调试信息，所以一般生成的`debug`文件会比`release`大一些。而`release`版本文件对程序做了较大的优化，因此文件会小一些。

而`gcc/g++`默认生成的可执行程序是`release`版本。但是`gdb`只能调试`debug`版本的文件，这个时候就需要给`gcc/g++`添加`-g`选项来生成`release`版本的文件。

```bash
$ cat main.c
#include <stdio.h>
int add(int num1, int num2)
{
    int sum = num1 + num2;
    return sum;
}
int main() 
{
    int num1 = 10;
    int num2 = 5;
    int sum = add(num1, num2);

    printf("num1: %d\n", num1);
    printf("num2: %d\n", num2);
    printf("sum: %d\n", sum);

    return 0;
}

$ gcc main.c a.out-release
$ gcc main.c -g a.out-debug
$ ls 
a.out a.out-g main.c
```

如果我们需要查看`release`文件中的调试信息，可以使用命令`readelf -S 文件名`来读取可执行程序的数据段格式，`-S`表示读取各个段，在下面可以查看到`debug`信息，而没有带`-g`选项生成的可执行程序则没有这一信息（这些试着操作一下，知道行）。

> 补充：再编译原理中，`Linux`中形成的可执行程序格式不是立刻全部变成二进制的，而是变成`ELF`格式，内部含有各种数据段...

利用管道工具可以让我们更加清晰看到这一现象：

```bash
$ readelf -S a.out-release | grep -i debug
$ readelf -S a.out-debug | grep -i debug
  [27] .debug_aranges    PROGBITS         0000000000000000  00001061
  [28] .debug_info       PROGBITS         0000000000000000  00001091
  [29] .debug_abbrev     PROGBITS         0000000000000000  00001199
  [30] .debug_line       PROGBITS         0000000000000000  00001224
  [31] .debug_str        PROGBITS         0000000000000000  0000126c
```

# 2.gdb的基本操作

## 2.1.打开退出命令

1. 打开`gdb`调试界面：`gdb debug版的可执行程序`
   
   ```bash
   $ gdb a.out-debug
   GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-120.el7
   Copyright (C) 2013 Free Software Foundation, Inc.
   License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
   and "show warranty" for details.
   This GDB was configured as "x86_64-redhat-linux-gnu".
   For bug reporting instructions, please see:
   <http://www.gnu.org/software/gdb/bugs/>...
   Reading symbols from /home/ljp/LimouCodeFile/CodeFile_2/MyFile_2023_9_25/a.out-debug...done.
   (gdb) 
   ```

2. 退出`gdb`调试界面：使用`[ctrl + d]`或者`quit`（同时清空断点信息）

## 2.2.查看代码命令

1. `l/list [行号]`：为查看调试的代码，每次显示`10`行（`list 0`可以重头开始看源代码文本）
2. `l/list [函数名]`：列出某个函数的源代码上下文

## 2.3.断点操作命令

1. `b/break [源文件名] 源代码行号`：设置断点，可以在某个源文件（因为有可能会有多个文件）的特定行号或函数名处设置断点（并且生成的断点编号一直是递增的）
2. `watch`设置观察点，当指定变量的值发生改变时中断程序执行（不过要在调试过程中才能设置）
3. `delete 断点/观察点编号`：删除断点/观察点，不是使用行号删除，而是删除断点对应的编号，删除所有断点就是`delete breakpoints`，注意这里就不需要指定文件，因为这里不是靠指定的行号，而是断点编号来取消断点的
4. `disable 断点/观察点编号`：禁用断点/观察点，不是使用行号删除，而是断点对应的编号，禁用所有断点就是`disable breakpoints`
5. `enable 断点编号/观察点`：启用断点/观察点，不是使用行号删除，而是断点对应的编号，开启所有断点就是`enable breakpoints`
6. `info/i b`：查看当前设置的所有断点/观察点。其中，查看断点是否被启用在使用命令`info b`里的`Enb`那一列，观察点通常被标记为 `hw watchpoint`等字样，而断点通常被标记为 `breakpoint`（这里还可以查看一个断点被命中的次数）

> 补充`1`：当您退出`gdb`的时候，所有的断点/观察点信息会被自动清除。
>
> 补充`2`：断点的禁用和启用也叫作“使能断点”。 

## 2.4.执行相关命令

1. `r/run` ：`run` 命令用于开始程序的执行。如果您在 `gdb` 中设置了断点或观察点，在使用 `run` 命令后，程序将会在第一个断点或观察点处暂停执行，此时您可以进行调试操作（例如单步执行、查看变量值等），类似`VS 2022`中第一次按下`[F5]`快捷键开始调试
2. `c/continue`：在使用`r`启用调试后，可以使用`c`来跳转下一个断点，类似`VS 2022`中继续使用`[F5]`快捷键
3. `n/next`：逐过程执行程序，但不进入函数内部，相当于`VS 2022`中的`[F10]`快捷键
4. `s/step`：逐语句执行程序，进入函数内部，相当于`VS 2022`中的`[F11]`快捷键
5. `unitl 行号`：可以跳转到行号出的代码，在使用`for`循环的时候很有用，不建议使用这个命令从`main()`跳到其他函数，这是一种局部性的跳转
6. `finish`：执行完当前函数并返回到调用者，并且停下来，这是一种全局性的跳转

## 2.5.程序状态命令

1. `display 变量名`：每次停在断点处时，自动显示指定变量的值，并且会显示对应的编号标识
2. `undisplay 变量编号`：则取消显示对应编号的变量的值（注意这里使用的是变变量编号）
3. `print(变量)或p 变量`：打印变量的值
4. `info locals`：查看当前函数/栈帧的所有局部变量
5. `info args`：查看当前函数的参数
6. `bt/backtrace`：显示当前的函数调用堆栈。`frame`切换到指定的堆栈帧。`up`切换到上一个堆栈帧，`down`切换到下一个堆栈帧
7. `x`：以十六进制格式查看内存内容

## 2.5.其他常用命令

1. `h/help`：获取帮助信息

2. `set var 变量名`可：以直接篡改修改某变量的值，有的时候需要查验在同一份代码下不同变量数据的表现

3. `show`：显示当前的设置参数

> 补充：由于`gdb`有很多重复操作，因此如果需要重复使用一个命令，在`gdb`中输入第一次命令后，往后只不断按需要回车即可重复使用该命令。

# 3.gdb的进阶操作

在设计多进程和线程代码中，还有很多相关的`gdb`命令，这些也值得看一下。
