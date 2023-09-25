# 1.debug和release

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

1. `l/list [行号]`为查看调试的代码，每次显示`10`行（`l 0`可以重头看代码文本）

2. `l/list [函数名]`列出某个函数的源代码

## 2.3.断点相关命令

1. `break/b [源文件名] 行号`设置断点，可以在某个源文件的特定行号或函数名处设置断点（并且生成的断点编号一直是递增的）

2. `delete 断点编号`删除断点/观察点，不是使用行号删除，而是删除断点对应的编号，删除所有断点就是`delete breakpoints`

3. `disable 断点编号`禁用断点/观察点，不是使用行号删除，而是断点对应的编号，这也叫作“使能断点”，禁用所有断点就是`disable breakpoints`

4. `enable 断点编号`启用断点/观察点，开启所有断点就是`enable breakpoints`

5. `info/i b`查看当前设置的断点/观察点。其中，查看断点是否被启用在使用命令`info b`里的`Enb`那一列

6. `watch`设置观察点，当指定变量的值发生改变时中断程序执行

## 2.4.执行相关命令

1. `r/run`开始执行程序

2. `c/continue`继续执行程序直到下一个断点或程序结束

3. `n/next`逐语句执行程序，但不进入函数内部

4. `s/step`逐语句执行程序，进入函数内部

5. `finish`执行完当前函数并返回到调用者，并且停下来，这是一种全局性的跳转

6. `unitl 行号`可以跳转到行号出的代码，在使用`for`循环的时候很有用，不建议使用这个命令从`main()`跳到其他函数，这是一种局部性的跳转。

## 2.5.程序状态命令

1. `print(变量)/p 变量`打印变量的值

2. `info locals`查看当前函数/栈帧的局部变量

3. `info args`查看当前函数的参数

4. `bt/backtrace`显示当前的函数调用堆栈。`frame`切换到指定的堆栈帧。`up`切换到上一个堆栈帧，`down`切换到下一个堆栈帧

5. `x`以十六进制格式查看内存内容。

6. `display 变量名`每次停在断点处时，自动显示指定变量的值。`undisplay 变量编号`取消显示对应编号的变量的值

## 2.5.其他常用命令

1. `help/h`获取帮助信息。

2. `set`修改变量的值。

3. `show`显示当前的设置参数。

> 补充：如果需要重复使用一个命令，在输入第一次命令后，往后只不断按需要回车即可实现重复使用该命令。
